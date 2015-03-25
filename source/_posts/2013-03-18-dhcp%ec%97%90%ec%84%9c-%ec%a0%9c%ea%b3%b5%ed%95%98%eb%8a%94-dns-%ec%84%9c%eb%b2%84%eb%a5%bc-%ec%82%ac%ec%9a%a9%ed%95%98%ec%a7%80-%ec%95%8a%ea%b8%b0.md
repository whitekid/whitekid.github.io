---
id: 1227
title: dhcp에서 제공하는 dns 서버를 사용하지 않기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1227
permalink: /archives/1227
dsq_thread_id:
  - 1146105022
tags:
  - dhclient
---
dhcp를 사용한다면 특별한 이유가 없는 한 dhcp server에서 제공하는 dns server를 사용합니다. 그런데 이 dns server가 아닌 다른 server를 사용하고자 한다면... 처음으로 /etc/resolv.conf를 직접 수정하는 방법을 생각할 것입니다.

그런데 이 방법의 문제는 /etc/resolv.conf에도 설명이 달린 것 처럼 dhcpclient가 renew 되었을 경우 수정한 내용이 덥혀쓰여진다는 것이지요.

이를 해결하는 방법은 dhclient.conf에 아래와 같이 사용할 dns server를 명시해주면 됩니다.

/etc/dhcp/dhclient.conf:

    prepend domain-name-servers <custom-dns-server>;

이렇게 설정된 dns는 /etc/resolv.conf에서 첫번재 entry로 등록이 됩니다. 이 옵션은 중복으로 사용 가능합니다.