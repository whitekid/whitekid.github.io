---
id: 807
title: su 명령을 wheel 그룹만 허용하기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=807
permalink: /archives/807
dsq_thread_id:
  - 716451978
categories:
  - Uncategorized
tags:
  - linux
---
[code]  
$ chown root.wheel /bin/su  
$ chmod 4750 /bin/su  
$ chattr +i /bin/su  
[/code]

근데 말이다.. su 명령은 아예 막고, sudo만 사용하게 하는건 어떨까?