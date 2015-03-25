---
id: 1024
title: socks5 proxy with ssh
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1024
permalink: /archives/1024
dsq_thread_id:
  - 899597479
categories:
  - Uncategorized
tags:
  - proxy
  - ssh
---
오늘 ssh가 sock5 proxy로도 사용될 수 있다는 사실을 알았다. 예전엔 [ss5][1]를 쓴 기억이 있어서 우분투에도 찾아봤는데, 이거 왠걸 여기는 패키지가 없다. 그래서 ubuntu에도 사용할 괜찮을 sock5 프록시 서버가 뭐가 있을가 하고 찾아봤는데 떡하니 ssh를 사용하는 방법이.. 게다가 당연히 별도의 설정도 거의 필요 없었다.

프록시 서버는 sshd만 돌고 있으면 된다. 내 데스크탑이 ubuntu이니 당연히 사용중이니 설정 건드릴 것 없고, 사용하려는 컴퓨터에서 프록시 서버로 사용할 컴퓨터로 ssh 접속을 아래와 같이 한다.

    $ ssh -D 9999 <user@192.168.xxx.xxx>  

이제 프록시 연결은 끝났다. 프록시를 사용할 컴퓨터에서 아래처럼 설정하고 사용하면 된다.

  * 프록시 타입: SOCKS v5
  * 호스트: 127.0.0.1
  * 포트: 9999

아주 간단~

참 혹시나 sshd 설정에서 AllowTcpForwarding true로 설정이 되어있는지 확인하시기 바랍니다. 기본값은 true이지만 배포판에 따라서 설정이 되어있을 수 있어요.

ps. 왜 이게 필요하냐고? 회사에서는 데스크탑 용 IP와 노트북용 IP를 별도의 영역으로 구분해 할당해 줬습니다. 노트북용 아이피는 또한 외부 방문자도 사용하는 것을 염두해두고 있어서 내부 개발 서버에 접근하는 것을 막아놨죠.

녜.. 데스크탑은 개발 서버에 접속이 되지만, 노트북은 안됩니다. 그래서 데스크탑을 프록시 서버로 사용해 접속하는 꽁수를... ㅎㅎ

참고

  * <http://embraceubuntu.com/2006/12/08/ssh-tunnel-socks-proxy-forwarding-secure-browsing/>

 [1]: http://ss5.sourceforge.net/