---
id: 903
title: 간단하게 그려본 OpenStack VM network traffic flow
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=903
permalink: /archives/903
dsq_thread_id:
  - 733725408
tags:
  - OpenStack
---
아주 아주 간단하게 그려본 OpenStack Guest VM Network Flow

{% img https://docs.google.com/drawings/pub?id=1KQnKfDgdUuO8V7GqvqWRgApenHIOlq8HIN3I3b_DeVc&w=960&h=720 %}

  * FloatDHCP 모드임
  * fixed_range로 지정된 VM에만 해당
  * Guest traffic은 nova-network에서 NAT을 통해서 나가도록 되어있음