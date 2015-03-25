---
id: 997
title: 'vmware 사용중에 vmnet 간에 routing이 안되는 문제가 발생할 때...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=997
permalink: /archives/997
dsq_thread_id:
  - 896223568
tags:
  - OpenStack
  - vmware
---
OpenStack folsom + Quantum 구성을 vmware을 이용하여 테스트 하고 있습니다.

  * vmnet0: external network
  * vmnet1: control node network
  * vmnet2: compute node network
  * vmnet3: internal data network
  * vmnet4: network node network
  * vmnet5: storage network(not done)

이렇게 대략 6개 정도 사용하는데, 가상 머신이 만들어 내부 네트워킹을 시도하면 routing이 안되는 문제가 발생합니다. 물론 외부랑 정상적이고, 또한 host 컴퓨터에서도 해당 머신들에 정상적으로 ping 가는데도 말입니다..

결국은 리눅스 네트워크 전문가를 대동하여 약1시간에 걸쳐서 확인한 결과 iptables 쪽에서 막은 건 아니고 vmnet간의 routing이 안된다는 결론을 얻었고, rp_filter 기능을  사용하지 않으면 되는군요.

    $ sysctl -a | grep vmnet | grep \\.rp_filter | awk '{print $1}' | \
    sudo xargs -L1 -I% sysctl %=0

  * \\ 두개입니다. 하나만 하면 arp_filter도 먹어버림...