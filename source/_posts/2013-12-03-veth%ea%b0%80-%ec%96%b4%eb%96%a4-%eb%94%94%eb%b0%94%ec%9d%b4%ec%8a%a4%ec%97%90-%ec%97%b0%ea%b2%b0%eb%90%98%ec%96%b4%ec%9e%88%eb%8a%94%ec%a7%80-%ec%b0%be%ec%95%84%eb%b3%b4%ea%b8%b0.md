---
id: 1427
title: veth가 어떤 디바이스에 연결되어있는지 찾아보기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1427
permalink: /archives/1427
dsq_thread_id:
  - 2019200825
tags:
  - OpenStack
  - veth
---
Quantum으로 네트워크를 구성하다보면(또는 XLC...) veth를 통해서 다른 network namespace에 interface가 peer로 역이는 것을 볼 수 있습니다.

> Network Namespace는 [Introducing Linux Network Namespaces][1] 글을 참조하시면 좋습니다. 간단히 말에서 device, routing, iptables, socket 등을 포함하여 linux network stack이 완전히 격리되는 시스템입니다. namespace를 만들어 network stack을 격리시키면 외부랑 완전히 차단이 되기 때문에 root namespace의 interface와 veth로 pair를 맺어 이 interface로 통신합니다.

> veth는 참고할 만한 문서를 딱히 못찾았는데요.. interface link의 클론이라고 생각하시면 됩니다. 한쪽에 들어간 패킷은 다른 한쪽으로 흐릅니다. 그리고 당연히 각각이 interface이기 때문에 mac, ipaddress가 다를 수 있습니다. 어떻게보면 bridge와도 비슷합니다.

근데 문제는 veth로 만드는 것은 쉽게 알 수 있는데, 해당 인터페이스가 어떤 인터페이스랑 peer인지 알아내는 방법이 쉽지 않습니다. iproute로는 말이지요.

아래는 quantum으로 구성한 dhcp namespace의 interface 입니다.

    # ip netns exec qdhcp-fabc7ead-6132-4920-af57-2ad5e199e1c1 ip a
    59: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        inet6 ::1/128 scope host
           valid_lft forever preferred_lft forever
    68: ns-39d60faa-aa: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether fa:16:3e:98:e6:f3 brd ff:ff:ff:ff:ff:ff
        inet 10.10.100.2/24 brd 10.10.100.255 scope global ns-39d60faa-aa
        inet 169.254.169.254/16 brd 169.254.255.255 scope global ns-39d60faa-aa
        inet6 fe80::f816:3eff:fe98:e6f3/64 scope link
           valid_lft forever preferred_lft forever
    # ip netns exec qdhcp-fabc7ead-6132-4920-af57-2ad5e199e1c1 route -n
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    0.0.0.0         10.10.100.1     0.0.0.0         UG    0      0        0 ns-39d60faa-aa
    10.10.100.0     0.0.0.0         255.255.255.0   U     0      0        0 ns-39d60faa-aa
    169.254.0.0     0.0.0.0         255.255.0.0     U     0      0        0 ns-39d60faa-aa

여기서 routing table을 보면 ns-39d60faa-aa 인터페이스를 통해서 외부로 나가는 것으로 보이는데, namespace 이므로 외부와 직접 연결할 수 없습니다. 당연히 ns-39d60faa-aa는 root namespace의 interface와 veth peer를 이루고 있으며, peer interface를 통해서 외부와 통신합니다.(그 peer는 bridge를 통해서 eth#으로 나가겠지요..)

그럼 여기서 본론인 어떤 interface와 peer를 이루고 있는지 알아보려고 열심히 ifconfig 또는 ip link 명령을 뒤져봐야 안나됩니다. mac도 다르고, peer 정보도 안나오고...

찾는 방법은 조금 복잡하기도 한데... ethtool을 통해서 찾을 수 있습니다.

    # ip netns exec qdhcp-fabc7ead-6132-4920-af57-2ad5e199e1c1 \
      ethtool -S ns-39d60faa-aa
    NIC statistics:
         peer_ifindex: 69

결과에서 예상하다시피 interface index가 69번인 interface와 peer를 이루고 있습니다. 그럼 아래처럼 찾을 수 있겠습니다.

    # ip link ls | grep ^69
    69: tap39d60faa-aa: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    # ip link show tap39d60faa-aa
    69: tap39d60faa-aa: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
        link/ether 62:41:1f:e2:b2:9d brd ff:ff:ff:ff:ff:ff
    # ethtool -S tap39d60faa-aa
    NIC statistics:

마찬가지로 tap39d60faa-aa interface는 namespace에 있는 68번 interface와 veth peer로 연결되어 있습니다.

지금은 필요없을 지 몰라도 OpenStack 하다보면 언젠가는 마주칠지 모르는 녀석이니 참고하세용..

ps. iproute 사용법은 너무 불친절합니다.

 [1]: http://blog.scottlowe.org/2013/09/04/introducing-linux-network-namespaces/