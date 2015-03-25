---
id: 1253
title: setup NAT on Mac OS X
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1253
permalink: /archives/1253
dsq_thread_id:
  - 1253787089
categories:
  - Uncategorized
tags:
  - nat
  - osx
---

    $ sudo sysctl -w net.inet.ip.forwarding=1  
    $ sudo natd -interface en0  
    $ sudo ipfw add divert natd ip from any to any via en0  
    