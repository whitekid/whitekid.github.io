---
id: 754
title: ssh tunneling
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=754
permalink: /archives/754
dsq_thread_id:
  - 725489027
categories:
  - Uncategorized
tags:
  - ssh
  - tunneling
---
기록은 기억보다 오래 기억된다는 측면에서

ssh -f -L 3307:dbdev.woosum.net:4444 <whitekid@woosum.net> sleep 864000

이렇게 하면 ssh로 woosum.net으로 접속하고 다시 여기서 dbdev.woosum.net:4444로 접속하여 tunneling을 127.0.0.1의 3307 포트에 만든다.

  * -f : background 작업으로
  * -L 3307 : 로컬에서 listen할 포트
  * dbdev.woosum.net : 접속할 호스트
  * 4444 : ssh-host에서 ssh로 접속해서 다시 접속할 호스트
  * <whitekid@woosum.net> : ssh로 접속할 정보
  * sleep 864000 : 접속해서 하루동안 sleep 실행, -f 옵션이 있으므로 연결하고 바로 command prompt로 떨어진다.

주의: 연결할 때 localhost 말고 127.0.0.1로 접속할 것.