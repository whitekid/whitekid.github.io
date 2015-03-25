---
id: 1223
title: dnsmasq는 /etc/hosts를 읽는다. 그리고 no-hosts 옵션
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1223
permalink: /archives/1223
dsq_thread_id:
  - 1145878480
categories:
  - Uncategorized
tags:
  - dnsmasq
---
dnsmasq는 dms forwarder로 개인적으로는 아주 간단한 DNS service(내부 개발 서비스)에 주로 사용합니다. 설치하고 /etc/dnsmasq.d/<domain> 파일이 아래처럼 넣으면 되니깐요.

    address=/chef.<domain>/192.168.xxx.xxx  
    address=/dns.<domain>/192.168.xxx.xxx  

bind처럼 아주 복잡한 형식의 설정 파일을 건드릴 필요없으니깐요.

위의 예제는 내부 개발 인프라의 dns service를 위해서 저렇게 만들었는데... 하나 문제가 생깁니다. 두번째 호스트는 dns service를 하는 호스트를 표시하는데, 클라이언트에서는 name lookup을 하면 아래처럼 의도하지 않은 결과를 얻게됩니다.

    $ nslookup dns.<domain> 192.168.xxx.xxx  
    Server: 192.168.xxx.xxx  
    Address: 192.168.xxx.xxx#53

    Name: dns.<domain>  
    Address: 127.0.0.1  

녜.. 의도하지 않게 127.0.0.1로 나오지요.. 이게 뭔지 한참 멍하니 쳐다봤습니다. 해당 호스트로 접속하려니 connnection refused... dnsmasq 설정에는 문제가 없는데 말입니다.

좀 뒤져보니.. dnsmasq는 /etc/hosts 파일을 읽고 그것도 같이 서비스합니다. 그래서 /etc/hosts 파일을 보면

    127.0.0.1 localhost  
    127.0.0.0 dns.<domain> dns  

녜.. 여기에서 설정된 호스트 이름이 dnsmasq에 의해서 서비스 되는 것입니다.

해결 방법은 2가지가 있습니다.

첫번째는 /etc/hosts 파일에 ip address 추가하는 겁니다. 127.0.0.1 대신 진짜 ip address를 넣는 것이지요.

    127.0.0.1 localhost  
    192.168.xxx.xxx dns.<domain> dns  

두번째 방법은 /etc/dnsmasq.conf에서 no-hosts 옵션을 추가하는 겁니다. no-hosts 옵션의 설명에는 아래처럼 되어 있습니다.

> If you don't want dnsmasq to read /etc/hosts, uncomment the follow line. 

첫번째 방법은 일반적은 /etc/hosts 파일의 사용법에 벗어납니다. 따라서 두번째 방법을 사용하면 됩니다.