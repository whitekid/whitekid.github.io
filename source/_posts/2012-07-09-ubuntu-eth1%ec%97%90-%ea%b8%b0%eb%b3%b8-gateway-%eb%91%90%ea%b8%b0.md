---
id: 928
title: 'ubuntu: eth1에 기본 gateway 두기..'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=928
permalink: /archives/928
dsq_thread_id:
  - 757371537
categories:
  - Uncategorized
tags:
  - Ubuntu
---
eth0, eth1이렇게 2개 NIC가 있는 상황에서 거의 기본으로는 eth0에서 public internet이 나가도록 설정이 될 겁니다. 대부분 그렇지요. 그런데 필요에 따라서는 eth1가 public internet인 경우가 있습니다. 그리고 eth0는 다른 목적으로 사용되는 경우로 말입니다.

이 경우 CentOS의 경우는 그냥 아무 생각없이 /etc/sysconfig/network-script/ifcfg-eth1에 설정하면 eth0 설정한거 무시되면서 eth1의 설정이 먹히는 데 Ubuntu의 경우는 안그럽니다.

    auto eth2  
    iface eth2 inet static  
    address 10.100.0.11  
    netmask 255.255.255.0  
    gateway 10.100.0.1  

이렇게 두면 이녀석이 eth2의 gateway 설정을 알어먹지 못합니다. 그래서 수동으로 해 줘아합니다. 근데 수동으로하면 골치아프죠. 그럴땐 post-up, pre-down으로..

    auto eth0  
    iface eth0 inet static  
    address 10.200.1.11  
    netmask 255.255.255.0  
    dns-nameservers 168.126.63.1

    auto eth1  
    iface eth1 inet static  
    address 10.100.0.11  
    netmask 255.255.255.0  
    post-up route add default gw 10.100.0.1  
    pre-down route del default gw 10.100.0.1  

이렇게 하면 되지요.