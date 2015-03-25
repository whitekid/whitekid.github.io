---
id: 478
title: 'ss5: socks proxy server for FreeBSD'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=478
permalink: /archives/478
dsq_thread_id:
  - 717583271
tags:
  - FreeBSD
  - proxy
  - ss5
---
많은 회사생활하는 분들도 그렇지만 여기 내가 일하고 있는 곳에 막혀있는 사이트가 많고 막혀있는 포트도 많다. 그래서 웹을 위해서는 이 서버에다 squid를 설치해서 우회해서 사용하고 있다.

그런데 말이다 인터넷이라는 것이 항상 웹으로만 되는건 아니라서.. 다른 포트가 필요할 경우가 있지요. 그래서 찾아보다가 socks proxy server를 설치해보기로 했다. [여기의 글][1]을 보고 그중에서 ss5가 가장 낳은것 같아서(아니면 좋은것 소개해 주세요.) 그것으로 해봤다.

## 설치

    $ portmaster net/ss5
    $ /usr/local/etc/rc.d/ss5 start

이렇게 해주면 단순히 설치가 끝나고 사용이 가능한다.

물론 /etc/rc.conf에 ss5_enable="YES" 를 추가하시구요.

## 우선 모두 다 열어

/usr/local/etc/ss5.conf에 다 열려면 아래 두 줄 확인할 것..

    auth 0.0.0.0/0 - -
    permit 0 0.0.0/0 - 0.0.0.0/0 - - - - -

## 기본 포트가 막혔군

socks 서버는 1080 포트를 사용한다. 근데 해보니.. 허걱 연결이 안되는군요. 포트를 바꿔야지요. 설정파일에는 그런게 없고 실행할때 파라미터로 주어집니다.

    /etc/rc.d에 ss5_flags="-b 0.0.0.0:<port>"

라고 적어주면 됩니다.

ㅋㅋ 이제 막인것도 다 뚫어! 단... application에서 proxy를 지원한다면 말입니다..

{% img /images/ie-socks-proxy-settings.png %}

 [1]: http://www.hm2k.com/posts/freebsd-socks-proxy-for-mirc
