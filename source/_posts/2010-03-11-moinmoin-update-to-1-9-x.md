---
id: 53
title: moinmoin update to 1.9.x
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=53
permalink: /archives/53
dsq_thread_id:
  - 718426628
categories:
  - Uncategorized
tags:
  - moinmoin
---
moinmoin이 1.9.x로 업데이트되었다. 업그레이드하는데 몇가지 절차가 필요하다.

<http://moinmo.in/RickVanderveer/UpgradingFromMoin18ToMoin19>

<http://hg.moinmo.in/moin/1.9/raw-file/1.9.1/docs/UPDATE.html>

진행하다보니... 1.8을 사용하고 있음에도 cgi로 사용하고 있었다. wsgi로 바꾸니 빨라졌다.