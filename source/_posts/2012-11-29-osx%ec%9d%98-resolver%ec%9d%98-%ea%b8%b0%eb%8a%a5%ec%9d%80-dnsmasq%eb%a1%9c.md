---
id: 1163
title: OSX의 resolver의 기능은 dnsmasq로
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1163
permalink: /archives/1163
dsq_thread_id:
  - 949686238
tags:
  - dnsmasq
---
dnsmasq는 dns forwarder가 기본입니다. 그리고 또 심플한 dns 기능을 가지고 있습니다. 이 기능을 모르고 이전에 [twisted를 이용해서 dns proxy를 만들었지만][1], dnsmasq를 이용하면 보다 간단하게 설정이 가능하니 설명하지요.

첫번째는 특정 도메인을 특정 네임서버에 보내도록하는 예제입니다.

/etc/dnsmasq.d/company.zone:

    server=/dev.company.com/192.168.1.199
    server=/myteam.company.com/192.168.1.200

다음 예제는 local이라는 나만의 가상 도메인을 만들고 호스트를 지정하는 방법입니다. /etc/hosts에 설정하는 것과 비슷하죠.
/etc/dnsmasq/local.zone:

    address=/local-db.local/10.20.1.4
    address=/local-[www.local/10.20.1.5][2]

이렇게해서 dnspost는 역사속으로 사라지는군요.

 [1]: http://blog.woosum.net/archives/1040
 [2]: http://www.local/10.20.1.5