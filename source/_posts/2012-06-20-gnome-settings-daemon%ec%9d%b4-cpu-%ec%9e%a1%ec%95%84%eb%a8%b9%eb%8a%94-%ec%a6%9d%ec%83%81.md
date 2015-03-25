---
id: 897
title: gnome-settings-daemon이 CPU 잡아먹는 증상
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=897
permalink: /archives/897
dsq_thread_id:
  - 733471085
categories:
  - Uncategorized
tags:
  - Ubuntu
---
갑자기 컴퓨터가 느려져서 이상하다고 보고있는데 gnome-settings-daemon이 CPU르 엄청 먹는거다.. 게다가 자세히보니 키보드 Numlock 키가 켜졌다가 꺼졌다가...

<https://bugs.launchpad.net/ubuntu/>+source/gnome-settings-daemon/+bug/969359에 있는데로

<p id="yui_3_2_0_1_1340167277331967">
  sudo mv /usr/lib/<wbr>gnome-settings-<wbr>daemon-<wbr>3.0/keyboard.<wbr>gnome-settings-<wbr>plugin ~/</wbr></wbr></wbr></wbr></wbr>
</p>

하고 재부팅하니 조용하다...

Ubuntu 너 이럴래? 아니 Gnome인가?