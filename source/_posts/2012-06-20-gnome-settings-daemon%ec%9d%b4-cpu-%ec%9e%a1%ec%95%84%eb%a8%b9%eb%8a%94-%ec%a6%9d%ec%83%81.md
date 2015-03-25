---
id: 897
title: gnome-settings-daemon이 CPU 잡아먹는 증상
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=897
permalink: /archives/897
dsq_thread_id:
  - 733471085
tags:
  - Ubuntu
---
갑자기 컴퓨터가 느려져서 이상하다고 보고있는데 gnome-settings-daemon이 CPU르 엄청 먹는거다.. 게다가 자세히보니 키보드 Numlock 키가 켜졌다가 꺼졌다가...

https://bugs.launchpad.net/ubuntu/+source/gnome-settings-daemon/+bug/969359에 있는데로

    sudo mv /usr/lib/gnome-settings-daemon-3.0/keyboard.gnome-settings-plugin ~/

하고 재부팅하니 조용하다...

Ubuntu 너 이럴래? 아니 Gnome인가?