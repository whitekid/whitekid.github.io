---
id: 1467
title: openvswitch bridge에 vlan port 추가
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1467
permalink: /archives/1467
dsq_thread_id:
  - 2055716157
categories:
  - Uncategorized
tags:
  - openvswitch
  - vlan
---
OpenVSwitch는 기본으로 trunk 모드로 동작한다. 이 경우 특정 vlan에 대한 interface를 추가하는 작업은.. 아래처럼.

    $ ovs-vsctl add-port <bridge> <port> tag=<vlan-id> \  
        --- set Interface <port> type=internal  

ex  

    $ ovs-vsctl add-port br0 vlan1000 tag=1000 \  
        --- set Interface vlan1000 type=internal  
