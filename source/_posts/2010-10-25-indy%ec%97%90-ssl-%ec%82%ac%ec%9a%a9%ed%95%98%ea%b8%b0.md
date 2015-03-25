---
id: 518
title: Indy에 SSL 사용하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=518
permalink: /archives/518
dsq_thread_id:
  - 751979151
categories:
  - Uncategorized
tags:
  - Delphi
  - Indy
  - SSL
---
Indy 라이브러리는 자체적으로 SSL을 지원하지 않습니다. Third party로 SSL IO Handler를 지정해줘야하는데 OpenSSL을 이용하는 방법은 다음과 같습니다.

  1. <a rel="nofollow" href="http://www.indyproject.org/Sockets/ssl.en.aspx">Indy SSL</a>에서 미리 컴파일된 OpenSSL DLL을 다운로드 한다. 여기서는 <a rel="nofollow" href="http://indy.fulgan.com/SSL/">fulgan</a>의 DLL을 다운 받았다.
  2. 파일중에서 필요한 2개의 파일(libeay32.dll, ssleay32.dll)을 실행파일이 위치할 곳에 푼다.
  3. TIdHttp로 이용하는 코드는 다음과 같다.

[code lang="delphi"]  
FHTTP := TIdHTTP.Create(nil);  
FOpenSSLHandler := TIdSSLIOHandlerSocketOpenSSL.Create(FHTTP);

FHTTP.IOHandler := FOpenSSLHandler;  
FHTTP.Get('<https://mail.google.com>');</pre>  
[/code]

<http://www.woosum.net/wiki/Indy>