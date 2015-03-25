---
id: 818
title: 'munin: Call to accept timed out'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=818
permalink: /archives/818
dsq_thread_id:
  - 721898918
categories:
  - Uncategorized
tags:
  - munin
---
munin을 돌리고 있는데, 뭔가 이상한게 보인다. 그래프가 중간중간 끊기는거다... 그래 점검하는 호스트가 좀 많기로서니... 좀 힘들어한다는 느낌이 들었다.

여기저기 둘러봤지만 .. 글쎄 확실한 해결책이 없다. 결국은 perl 소스를 조금 보기로했지..

ProcessManager.pm에서 accept를 기다리는 timeout이 아래처럼 10으로 되어있는데, 간딘히 360으로 해놨다. 우선 로그보니 timeout 메시지는 안나온다.  
[code]  
#accept_timeout  => 10,  
accept_timeout  => 360,  
[/code]