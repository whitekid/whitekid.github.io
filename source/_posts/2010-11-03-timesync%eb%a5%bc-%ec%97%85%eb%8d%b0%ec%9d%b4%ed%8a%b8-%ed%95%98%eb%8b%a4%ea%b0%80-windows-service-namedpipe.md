---
id: 555
title: 'TimeSync를 업데이트 하다가.... Windows Service/ NamedPipe'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=555
permalink: /archives/555
dsq_thread_id:
  - 716307557
categories:
  - Uncategorized
tags:
  - Delphi
  - NamedPipe
  - Service
  - TimeSync
---
[TimeSync][1]가 Windows 7에서 동작하지 않았던 문제를 최근에서야 알고, 그 문제를 수정하면서 했던 일 몇가지 기록해봅니다.

## SetLocalTime과 권한

윈도우즈 시간 설정은 [SetLocalTime API][2]를 사용합니다. 이게 Windows Vista/7 에 오면서 보호된 시스템 리소스로 변경되면서 SE\_SYSTEMTIME\_NAME 권한을 필요로합니다. 관리자 권한으로 실행하도록 하면 되겠지만, TimeSync 원래 목적이 자동으로 시간을 맞추는 것였기 때문에 사용자에게 관리자 권한 물어보는 다이얼로그가 나오는 상황을 원치 않았습니다.

그래서 결국은 시간 동기화 서비스를 만들고, 클라이언트 프로그램으로 서비스를 호출하는 것으로 변경했습니다. 그렇습니다. 배보다 배꼽이 더 커졌습니다.

## Windows Service

### 서비스 등록 및 해지

서비스의 등록과 삭제는 관리자 권한이 필요합니다. 관리자 권한으로 서비스 프로그램에 옵션을 주어서 실행합니다.

<pre>ServiceApplication.exe /INSTALL [/SILENT]
ServiceApplication.exe /UNINSTALL [/SILENT]</pre>

### 서비스 시작 및 종료

<pre>net.exe start ServiceName
net.exe stop ServiceName</pre>

## Delphi로 Windows Service 작성하기

### 서비스 작성

Delphi로 서비스를 작성하는 것은 아래 참조 링크에 아주 자세히 설명이 되어있습니다. 서비스지만 서버라고 보시면 됩니다. 그래서 일반적인 서버를 작성하는 개념으로 작성해야합니다.

서비스는 서비스 Thread를 하나 만들고, 서비스의 각각의 이벤트에 해당 Thread를 컨트럴 해줍니다.

  1. OnServiceStart에 서비스 Thread 시작
  2. OnServiceStop에 서비스 Thread 종료
  3. OnServiceContinue, OnServicePause에 서비스 Thread Start/ Suspend
  4. OnServiceExecute에서 해당 요청을 처리하면 됩니다.

이렇게 해서 서비스의 대략적인 소스는 다음과 같습니다.

[code lang="delphi"]  
procedure TTimeSyncService.ServiceContinue(Sender: TService;  
var Continued: Boolean);  
begin  
ServiceThread.Start;  
Continued := True;  
end;

procedure TTimeSyncService.ServiceExecute(Sender: TService);  
begin  
while not Terminated do  
ServiceThread.ProcessRequests(False);  
end;

procedure TTimeSyncService.ServicePause(Sender: TService; var Paused: Boolean);  
begin  
ServiceThread.Suspended := True;  
Paused := True;  
end;

procedure TTimeSyncService.ServiceStart(Sender: TService; var Started: Boolean);  
begin  
TimeSyncServiceThread := TTimeSyncServiceThread.Create(False);  
Started := True;  
end;

procedure TTimeSyncService.ServiceStop(Sender: TService; var Stopped: Boolean);  
begin  
TimeSyncServiceThread.Terminate;  
Stopped := True;  
end;  
[/code]

이 서비스 Thread에서 클라이언트와 통신을 대기하면 됩니다. 처음 서비스를 작성하려고 생각했을때 서비스니깐 뭔가 클라이언트와 통신하는 방법을 기본으로 제공해줄까? 하고 생각을 했었지만 아무것도 없습니다. 그 방법은 작성하는 사람이 TCP/IP, PIPE, MemoryMap 등등 [제공하는 IPC][3] 중에서 편한것 선택해서 하면 됩니다.

여기서는 그냥 가장 간단할 것 같은(하지만 또 다른 복병때문에 삽질 했지만요)  NamedPipe를 이용했습니다.

### Service Thread

TimeSync는 여러 크라이언트의 동시 연결 또는 여러 복잡 다단한 명령들이 필요하지 않기에 요청이 있으면 바로 수행하고 연결을 끊어버리는 구조로 구성했습니다. HTTP와 비슷하게요.

대락적인 코드는 다음과 같습니다.

[code lang="delphi"]  
procedure TTimeSyncServiceThread.Execute;  
begin  
// 통신할 파이프 생성  
PipeHandle := CreateNamedPipe(PIPE_NAME,  
PIPE\_ACCESS\_DUPLEX,  
PIPE\_TYPE\_MESSAGE or PIPE\_READMODE\_MESSAGE or PIPE_WAIT,  
PIPE\_UNLIMITED\_INSTANCES,  
BUFSIZE,  
BUFSIZE,  
NMPWAIT\_USE\_DEFAULT_WAIT,  
@sa);

try  
while not Terminated do  
begin  
// 서비스 클라이언트의 연결 대기  
ConnectNamedPipe(PipeHandle, nil);

// 클라이언트 패킷 수신  
FileRead(PipeHandle, Buffer, ....);

// 클라이언트의 요청에 따라서 뭔가 수행  
DoSometing(Buffer);

// 처리 결과를 클라이언트에 보내기  
FileWrite(PipeHandle, Buffer, ....);

// 연결 해제하고 다른 클라이언트의 연결을 기다림...  
DisconnectNamedPipe(PipeHandle);  
end;  
finally  
CloseHandle(PipeHandle)  
end;  
end;  
[/code]

클라이언트에서 마찬가지로 파이프에 연결해서 처리합니다.

[code lang="delphi"]  
procedure Sync;  
var  
PipeHandle: THandle;  
PipeStream: THandleStream;  
Command, Result: DWORD;  
begin  
PipeHandle := CreateFile(PIPE_NAME,  
GENERIC\_READ or GENERIC\_WRITE, 0, nil, OPEN_EXISTING, 0, 0);

// 연결할 파이프가 부족하면 잠시 대기합니다. 하지만 TimeSync에서는 가능성이 극히 적음  
if GetLastError = ERROR\_PIPE\_BUSY then  
begin  
if not WaitNamedPipe(PIPE_NAME, 2000) then  
raise Exception.Create(StrServiceConnectFailed);  
end;  
try  
FileWrite(PipeHandle, Buffer, ....);  
FileRead(PipeHandle, Buffer, ....);  
finally  
DisconnectNamedPipe(PipeHandle);  
CloseHandle(PipeHandle);  
end;  
end;  
[/code]

아주 아주 간단한 구조로 구성이 되었고, 잘 될거라고 믿고 있었습니다.

### Named Pipe로 서비스와 통신하기

하지만 항상 복병이 있지요. 우성 서비스로 작성하기 전에 일반 어플리케이션으로 파이프 서버 작성하고 했더니 잘 되어서 서비스로 적용하는데, 파이프로 연결하는 부분부터 안됩니다. 연결 자체가 안됩니다.

원인은 파이브를 기본으로 만들면 그 프로세스를 생성한 사용자만 그 파이브에 접근할 수 있습니다. Service는 관리자 권한으로 실행되는 것이니, 당연히 일반 사용자로 실행되는 클라이언트의 접근을 거부해버립니다.

그래서 서버의 파이프를 생성할때 CreateNamedPipe의 마지막 인자로 SecurityAttribute를 설정해야합니다. 아래는 누구나 다 그 만들어진 파이프에 연결이 가능하게 하는 SecurityAttribite를 만드는 코드입니다.

[code lang="delphi"]  
var  
PipeHandle: THandle;  
sa: TSecurityAttributes;  
sd: TSecurityDescriptor;  
begin  
InitializeSecurityDescriptor(@sd, SECURITY\_DESCRIPTOR\_REVISION);  
SetSecurityDescriptorDacl(@sd, True, nil, False);

FillChar(sa, SizeOf(sa), 0);  
sa.nLength := SizeOf(sa);  
sa.lpSecurityDescriptor := @sd;  
sa.bInheritHandle := False;

PipeHandle := CreateNamedPipe(PIPE_NAME,  
PIPE\_ACCESS\_DUPLEX,  
PIPE\_TYPE\_MESSAGE or PIPE\_READMODE\_MESSAGE or PIPE_WAIT,  
PIPE\_UNLIMITED\_INSTANCES,  
BUFSIZE,  
BUFSIZE,  
NMPWAIT\_USE\_DEFAULT_WAIT,  
@sa);  
[/code]

이렇게 했더니만 연결이 잘 됩니다. 이제 프로그램은 완성했습니다. ^^;

## InnoSetup으로 설치 스크립트 만들기

이렇게 만들고 보니 프로그램을 실행하기 위해서 먼저 해결해야할 것이 있네요.

  1. 서비스 등록
  2. 서비스 실행
  3. 클라이언트 시작프로그램에 등록

이걸 개인이 하라고 하는건 무린것 같고(특히 서비스 등록이나 실행은 관리자 권한이 필요하죠). 그래서 [InnoSetup][4]으로 설치 스크립트를 만들었습니다.

<pre>[Run]
Filename: net.exe; Parameters: stop TimeSyncService
Filename: {app}\TimeSyncSvc.exe; Parameters: /INSTALL /SILENT
Filename: net.exe; Parameters: start TimeSyncService

[UninstallRun]
Filename: net.exe; Parameters: stop TimeSyncService
Filename: {app}\TimeSyncSvc.exe; Parameters: /UNINSTALL /SILENT

[Icons]
Name: {commonstartup}\시간 동기화; Filename: {app}\TimeSync.exe; IconFilename: {app}\TimeSync.exe; Languages:</pre>

포인트는 설치중에 서비스 설치/ 실행하고, 설치 제거하면 서비스 정지/ 해제하는 것, 그리고 시작프로그램에 등록하는 것입니다.

## 기타

### 오류: "지정된 서비스가 지워진 것으로 표시되었습니다"

서비스를 잘못 시작/ 종료/ 삭제하다보면 위의 메시지가 나올때가 있습니다(지금 떠올려보면 서비스가 실행된 상태에서 서비스를 uninstall 했던 것 같습니다). 이것은 서비스가 말그대로 <a href="<http://www.pyrasis.com/blog/entry/ServiceDeleteFlag>">삭제 플레그가 설정된 것으로 시간이 지나면 자동으로 삭제된다</a>고 합니다.

하염없이 기다리시거나 아니면 재부팅하면 됩니다. 한없이 기다리기 뭐해서 재부팅하고 커피한잔 마셨습니다.

### 서비스를 실행했는데, 파이프가 만들어 졌을까?

이런 고민이 있을 수 있습니다. 서비스 디버깅 하면 되긴하지만, 간단한 것에 귀찮아서 안했고, 그냥 파이프가 만들어 졌는지만 확인하려고 했습니다.

파이프가 만들어진 것을 확인하는 툴은 [SysInternalsSuite][5]에 있는 pipelist.exe 툴을 이용하면 확인 가능합니다.

TimeSync_Pipe가 여기서 사용하는 파이프입니다.

[<img class="aligncenter size-full wp-image-580" title="pipelist" src="http://blog.woosum.net/wp-content/uploads/2010/11/pipelist.png" alt="" width="677" height="410" />][6]

## 참고 자료

  * <http://www.devarticles.com/c/a/Delphi-Kylix/Creating-a-Windows-Service-in-Delphi/>
  * <http://delphi.about.com/od/windowsshellapi/a/delphi-windows-service-applications.htm>
  * <http://www.tolderlund.eu/delphi/service/service.htm>
  * <http://groups.google.com/group/microsoft.public.vc.language/browse_thread/thread/584464bab80972c5/f3b89572716b8dd0>
  * <http://answers.google.com/answers/threadview?id=430173>

 [1]: /timesync
 [2]: http://msdn.microsoft.com/en-us/library/ms724936%28VS.85%29.aspx
 [3]: http://msdn.microsoft.com/en-us/library/aa365574%28VS.85%29.aspx
 [4]: http://www.jrsoftware.org/isinfo.php
 [5]: http://technet.microsoft.com/en-us/sysinternals/bb842062.aspx
 [6]: http://blog.woosum.net/wp-content/uploads/2010/11/pipelist.png