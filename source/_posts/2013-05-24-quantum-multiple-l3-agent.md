---
id: 1273
title: 'Quantum: multiple l3-agent'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1273
permalink: /archives/1273
dsq_thread_id:
  - 1313098193
categories:
  - Uncategorized
tags:
  - OpenStack
  - Quantum
---
grizzly에는 multiple quantum agent가 된다는 정보가 있습니다.

l3-agent가 돌아가는 곳은 모든 네트워크 트래픽이 통과하기에 SPoF의 문제를 해결해야하는데, multiple quantum agent가 된다면 하나의 agent가 문제가 생겼을 경우 다른 agent로 traffic을 전달하여 failover를 수행할 수 있기에 테스트 해 봤습니다.

단순하게 기존의 network 노드와 똑같이 하나 더 network 노드를 추가한 후에 상황은 다음과 같습니다.

    root@control:~# quantum agent-list
    +--------------------------------------+--------------------+-----------------+-------+----------------+
    | id                                   | agent_type         | host            | alive | admin_state_up |
    +--------------------------------------+--------------------+-----------------+-------+----------------+
    | 0665ee95-55b4-4f7a-a647-e1cf75c019b3 | Open vSwitch agent | network02.stack | :-)   | True           |
    | 3d324ac7-a900-4ee9-b97a-df43a30a14e2 | Open vSwitch agent | network.stack   | :-)   | True           |
    | 405275c2-48e2-43e4-9853-ce314976dc61 | DHCP agent         | network02.stack | :-)   | True           |
    | 6d47b5ce-9d07-4fdb-ad2d-239123ad4087 | L3 agent           | network02.stack | :-)   | True           |
    | 91b431b0-ece9-4fbb-a0b9-757c22034ffc | Open vSwitch agent | compute01.stack | :-)   | True           |
    | a4887a55-6baf-43ad-a0fa-fd67a07181f2 | DHCP agent         | network.stack   | :-)   | True           |
    | da17342b-d2cb-494a-9cf9-5a264b814dde | L3 agent           | network.stack   | :-)   | True           |
    | f6d3146a-7a9e-4483-8cbd-c76ef1c76081 | Open vSwitch agent | compute02.stack | :-)   | True           |
    +--------------------------------------+--------------------+-----------------+-------+----------------+

여기서 보면 network.stack과 network02.stack에 같은 쌍의 quantum agent가 동작하고 있습니다.

    root@control:~# quantum l3-agent-list-hosting-router router_admin_ext
    +--------------------------------------+---------------+----------------+-------+
    | id                                   | host          | admin_state_up | alive |
    +--------------------------------------+---------------+----------------+-------+
    | da17342b-d2cb-494a-9cf9-5a264b814dde | network.stack | True           | :-)   |
    +--------------------------------------+---------------+----------------+-------+

다시 확인하면 admin tenant의 router인 router\_admin\_ext 라우터는 network.stack에서 동작하는 것을 확인할 수 있습니다. 물론 network.stack 노드에서도 아래처럼 l3 agent가 정상적으로 설정되어 있는 것을 확인할 수 있습니다.

    root@network:~# ip netns
    qrouter-f3fe0c48-3248-4b2d-9f5b-c4a9c5fc01ed
    qdhcp-85e58593-f869-4233-9e5f-26e7b63df016
    root@network:~# ip netns exec qrouter-f3fe0c48-3248-4b2d-9f5b-c4a9c5fc01ed ip addr
    15: qr-8acb1ecd-d0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
        link/ether fa:16:3e:74:31:5d brd ff:ff:ff:ff:ff:ff
        inet 10.250.0.1/24 brd 10.250.0.255 scope global qr-8acb1ecd-d0
        inet6 fe80::f816:3eff:fe74:315d/64 scope link 
           valid_lft forever preferred_lft forever
    16: qg-a364f00b-f1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
        link/ether fa:16:3e:5e:80:f9 brd ff:ff:ff:ff:ff:ff
        inet 10.200.0.2/16 brd 10.200.255.255 scope global qg-a364f00b-f1
        inet6 fe80::f816:3eff:fe5e:80f9/64 scope link 
           valid_lft forever preferred_lft forever
    17: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        inet6 ::1/128 scope host 
           valid_lft forever preferred_lft forever

이제 이 router를 network02.stack으로 옮겨보죠. 옮기는 것도 자동으로 되면 좋겠는데, 아직 그러한 기능은 없는 것으로 보이며 l3-agent-router-remove, l3-agent-router-add 명령을 이용해서 옮기면 됩니다.

    root@control:~# quantum l3-agent-router-remove da17342b-d2cb-494a-9cf9-5a264b814dde router_admin_ext
    Removed Router router_admin_ext to L3 agent

이렇게 하면 network.stack에서 동작하는 l3-agent에서 router\_admin\_ext를 제외합니다. 이 명령을 내린 후에 network.stack 노드에서 해당 namespace를 확인하면 ip address 설정들이 모두 삭제되어 있습니다.

이제 network02.stack l3-agent에 router\_admin\_ext를 할당하면 됩니다.

    root@control:~# quantum l3-agent-router-add 6d47b5ce-9d07-4fdb-ad2d-239123ad4087 router_admin_ext
    Added router router_admin_ext to L3 agent
    root@control:~# quantum l3-agent-list-hosting-router router_admin_ext
    +--------------------------------------+-----------------+----------------+-------+
    | id                                   | host            | admin_state_up | alive |
    +--------------------------------------+-----------------+----------------+-------+
    | 6d47b5ce-9d07-4fdb-ad2d-239123ad4087 | network02.stack | True           | :-)   |
    +--------------------------------------+-----------------+----------------+-------+

이렇게 하면 옮겨지는 네트워크 노드인 network02.stack에 router에 해당하는 network namespace가 생기고, 그리고 여기에 l3-agent가 동작할 수 있는 설정이 생깁니다.

    root@network02:~# ip netns
    qrouter-f3fe0c48-3248-4b2d-9f5b-c4a9c5fc01ed
    root@network02:~# ip netns exec qrouter-f3fe0c48-3248-4b2d-9f5b-c4a9c5fc01ed ip addr
    12: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        inet6 ::1/128 scope host 
           valid_lft forever preferred_lft forever
    13: qr-8acb1ecd-d0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
        link/ether fa:16:3e:74:31:5d brd ff:ff:ff:ff:ff:ff
        inet 10.250.0.1/24 brd 10.250.0.255 scope global qr-8acb1ecd-d0
        inet6 fe80::f816:3eff:fe74:315d/64 scope link 
           valid_lft forever preferred_lft forever
    14: qg-a364f00b-f1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN 
        link/ether fa:16:3e:5e:80:f9 brd ff:ff:ff:ff:ff:ff
        inet 10.200.0.2/16 brd 10.200.255.255 scope global qg-a364f00b-f1
        inet6 fe80::f816:3eff:fe5e:80f9/64 scope link 
           valid_lft forever preferred_lft forever

이상으로 multiple quantum agent의 간단한 기능을 확인해 봤습니다. 이 기능을 보면서 자동 failover/ load balancing 등의 기능이 있으면 좋겠구나 했지만 그런 기능은 아쉽지만 없네요. 하지만 간단한 작업으로 손쉽게 추가할 수 있을 것으로 보입니다.

update) 이를 자동화한 스크립트 https://gist.github.com/whitekid/5642608#file-os-router-move-sh