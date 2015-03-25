---
id: 1182
title: 'kvm: linux bridge 대신 openvswitch  사용하기'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1182
permalink: /archives/1182
dsq_thread_id:
  - 2043084384
categories:
  - Uncategorized
tags:
  - bridge
  - kvm
  - openvswitch
---
/etc/default/openvswitch-switch  
[code]  
BRCOMPAT=yes  
[/code]

bridge 호환 모듈, 데몬  
[code]  
$ apt-get install openvswitch-brcompat  
[/code]