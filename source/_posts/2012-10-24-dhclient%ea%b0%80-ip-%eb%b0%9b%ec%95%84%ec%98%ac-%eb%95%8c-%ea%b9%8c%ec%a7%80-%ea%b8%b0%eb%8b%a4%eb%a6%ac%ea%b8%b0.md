---
id: 1013
title: 'dhclient가 ip 받아올 때 까지 기다리기...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1013
permalink: /archives/1013
dsq_thread_id:
  - 901116613
tags:
  - bash
---
dhcp로 아이피 받아올 때 까지 기다려서 ip 리턴하는 스크립트...

    #!/bin/bash
    function get_dhcp_ip() {
      IFACE=${1:-eth1}

      until ifconfig ${IFACE} | grep 'inet addr' > /dev/null; do
          dhclient ${IFACE} > /dev/null;
          sleep 1;
      done;

      ifconfig ${IFACE} | grep 'inet addr' | cut -d : -f 2 | awk '{print $1}'
    }

    echo $(get_dhcp_ip eth1)

왜 이런 삽질이 필요한지.. ㅡㅡ;