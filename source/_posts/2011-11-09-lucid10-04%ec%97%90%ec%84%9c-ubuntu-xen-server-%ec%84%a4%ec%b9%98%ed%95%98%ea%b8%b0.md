---
id: 782
title: Lucid(10.04)에서 ubuntu-xen-server 설치하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=782
permalink: /archives/782
dsq_thread_id:
  - 716627974
tags:
  - Xen
---
Lucid에서는 공식적으로 Xen을 지원하지 않는다.

아래 페이지에 나온 PPA를 설정해서 Xen을 설치해야한다. 대략적은 과정은 다음과 같다.

/etc/apt/sources.list에 PPA 추가

    deb http://ppa.launchpad.net/xtaran/xen-tools/ubuntu lucid main
    deb-src http://ppa.launchpad.net/xtaran/xen-tools/ubuntu lucid main

    sudo apt-get install python-software-properties
    sudo add-apt-repository ppa:xtaran/xen-tools
    sudo apt-get update
    sudo apt-get install ubuntu-xen-server

https://help.ubuntu.com/community/Xen#Installation