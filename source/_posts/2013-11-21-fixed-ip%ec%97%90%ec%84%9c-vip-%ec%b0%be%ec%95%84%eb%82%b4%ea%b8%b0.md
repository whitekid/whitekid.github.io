---
id: 1423
title: 'fixed ip에서 vip 찾아내기...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1423
permalink: /archives/1423
dsq_thread_id:
  - 1984690756
tags:
  - Quantum
---
Quantum으로 인스턴스의 fixed ip를 기준으로 해당 인스턴스의 load balancer에 연결된 vip 얻어오기

{% gist 7576376 %}

  * overlapping ip 환경에서는 안됨... network을 구분하지 못해서...

