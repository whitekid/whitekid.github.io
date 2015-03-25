---
id: 802
title: 'keepalived를 설정하는데... vrid가 중복이네??'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=802
permalink: /archives/802
dsq_thread_id:
  - 715346746
tags:
  - keepalived
---
keepalived를 설정하는데 receive an invalid ip number count associated with VRID! 라는 로그가 계속 나온다. 말그대로 VRID로 받은 패킷의 IP 갯수와 설정된 IP 갯수가 다르다는데, 엇뜬 이해하기가 힘들다.

고민해보니깐.. VRID가 중복된 것 아닐까? 이 네트웍에 다른 vrrp가 동작하는 것이 아닐까하고 VRID를 바꾸니까 동작한다.

금방 바꿔서 문제를 해결했지만, 이런 문제를 사전에 인지하려면 뭔가 방법이 필요했다. 현제 네트웍에서 사용중인 VRID를 모두 알 수 없을까??? 여기저기 자료를 찾아봤지만, 이런 문제에 대한 직접적인 해결 방법은 찾을 수 없었다.

그러다가 눈에 띈게 [vrrp 프로토콜은 224.0.0.18로 boradcasting한다][1]는 사실.  그리고 keepalived에서 vrrp는 기본 설정상 1초에 1번 vrrp advertising을 한다는 것.. 라서 해당 224.0.0.18와 통신하는 것을 패킷을 보면 vrid가 나오겠구나 생각하고 해봤는데..

    $ sudo tcpdump -i eth0 host 224.0.0.18
    tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
    listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
    19:17:30.574665 IP 192.168.0.199 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 1, prio 101, authtype simple, intvl 1s, length 28
    19:17:30.574676 IP 192.168.0.199 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 1, prio 101, authtype simple, intvl 1s, length 28
    19:17:30.574770 IP 192.168.0.199 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 2, prio 101, authtype simple, intvl 1s, length 24
    19:17:30.574773 IP 192.168.0.199 > vrrp.mcast.net: VRRPv2, Advertisement, vrid 2, prio 101, authtype simple, intvl 1s, length 24

다행이도 생각했던 것 처럼 나온다. 그래서 아래처럼 5초 동안 VRIP를 감사하면 현재 네트웍의 VRIP가 다 나오고 여기서 없는 것으로 피해가면 된다!!

    sudo timeout 5 tcpdump -i eth0 host 224.0.0.18 2>&1 | grep vrid | awk '{print $9}' | sort | uniq

 [1]: http://en.wikipedia.org/wiki/Multicast_address#IPv4