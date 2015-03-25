---
id: 972
title: screen으로 여러 윈도우 한번에 열기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=972
permalink: /archives/972
dsq_thread_id:
  - 858124545
categories:
  - Uncategorized
tags:
  - bash
  - screen
---
screen을 이용해 여러 윈도우를 한번에 열고 각각의 윈도우에서는 적당한 호스트를 연결하는 쉘 스크립트 입니다. [이전에 시도했던 것][1]에서 많은 문제가 발생해서 사용하지 않고 있었으나 이것은 좀 쓸만합니다.

    #!/bin/bash
    SCREENNAME=stack
    NL=`echo -ne '\015'`
     
    if screen -ls | egrep -q "[0-9].$SCREENNAME"; then
            echo "screen named $SCREEN_NAME already exists"
            exit
    fi
     
    screen -d -m -S $SCREENNAME -t shell
    sleep 1
     
    host_spec="host1/192.168.0.10 host2/192.168.0.11"
    for spec in $host_spec; do
            name=`echo "$spec" | cut -d '/' -f 1`
            host=`echo "$spec" | cut -d '/' -f 2`
     
            screen -S $SCREENNAME -X screen -t "$name"
            screen -S $SCREENNAME -p "$name" -X stuff "sshpass -ppassword ssh root@$host$NL"
    done
     
    echo "please run screen -r 'screen -r $SCREENNAME'"

물론 이것도 약간의 문제점이 있습니다. 명령을 실행하면 screen이 background에서 detach된 상태로 만들어 지는데, 이를 수동으로 attach해야하는 문제가 있습니다.

그래도 이전에 화면 깨지는 것보다는 훨씬 낫죠. ㅎㅎ

ps. 이 스크립트는 devstack의 stack.sh를 많이 참고했습니다.

 [1]: http://blog.woosum.net/2012/07/screen-session-%ec%a0%80%ec%9e%a5/