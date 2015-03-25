---
id: 1517
title: haproxy로 이상한 녀석들 거부하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1517
permalink: /archives/1517
dsq_thread_id:
  - 2686821042
tags:
  - haproxy
---
보안팀 공지가 떳다.. 공격툴로 공격하는 녀석들은 막아달라고.. 그런 툴들은 User-Agent에 친절하게 자기가 누구라고 알리기 때문에 이 녀석만 막게 해달라고 말이지...

우리는 haproxy가 전면에 있기 때문에, 그 단에서 막기로 했는데, 생각보다 아주 심플하다.

    acl is-bad-agent hdr(Acunetix-Product) -m reg -i ^WVS
    acl is-bad-agent hdr(User-Agent) -m reg (ZmEu|paros|nikto|dirbuster|sqlmap|openvas|w3af|Morfeus|JCE|Zollard)
    block if is-bad-agent

딸랑 3줄~