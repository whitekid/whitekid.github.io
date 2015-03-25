---
id: 554
title: 'TimeSync: Windows7에서 안되는 문제'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=554
permalink: /archives/554
dsq_thread_id:
  - 741609589
categories:
  - Uncategorized
tags:
  - MadeByKidd
  - TimeSync
---
[SyncTime 이라는 컴퓨터의 시간을 타입서버와 맞춰주는][1] 개인적으로 만든 프로그램이 있었지요.

그런데 Windows 7으로 업그레이드한 후 작동을 하지 않습니다. 아주아주 간단한 프로그램인데 말입니다.

뒤져보니 SetLocalTime API가 SE\_SYSTEMTIME\_NAME 이라는 권한을 필요하다고 하네요. Windows Vista 이후 부터는 시간이라는 것도 시스템 자원이라는 개념으로 들어갔다는군요.

그래서 이 권한을 획득하고 처리했는데... 안됩니다. 뭐냐고.. Indy에서도 확인해보니 SE\_SYSTEMTIME\_NAME 권한을 획득하고 처리하고 있지요. 하라는데로 다 했는데 SetLocalTime 호출하면 오류가 납니다.

뒤져보니 [사용자가 SetLocalTime을 호출할 이유가 없다][2]라는 답도 있었지만, 필요에 따라서는 시간을 맞춰줄 필요가 있는데 말입니다. 위 링크에 이야기 언급한 서비스로 만드는건 배보다 배꼽이 큽니다.

생각하다가 그냥 서비스까지 동원해서 작업해버리기로 했습니다. 덕분에 설치하는 것이 조금 복잡해 지긴 했습니다. 이렇게 복잡해지니 단순 실행파일만 가지고는 안되서 셋업본으로 만들었습니다.

설치 및 다운로드는 [TimeSync 페이지][3]를 보세요.

 [1]: /2010/03/%ec%8b%9c%ea%b0%84-%eb%8f%99%ea%b8%b0%ed%99%94%ed%95%98%ea%b8%b0/
 [2]: http://www.codeguru.com/forum/showpost.php?p=1757580&postcount=6
 [3]: /timesync