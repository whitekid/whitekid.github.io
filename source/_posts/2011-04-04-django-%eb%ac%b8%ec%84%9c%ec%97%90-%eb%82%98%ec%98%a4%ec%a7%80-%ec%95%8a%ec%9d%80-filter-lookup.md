---
id: 700
title: django 문서에 나오지 않은 filter lookup
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=700
permalink: /archives/700
dsq_thread_id:
  - 730740095
categories:
  - Uncategorized
tags:
  - django
---
<http://docs.djangoproject.com/en/1.3/topics/db/queries/#field-lookups> 에 필터에 사용할 수 있는 것들이 있는데, 여기에 나와있지 않는 것들이 있다. django가 문서가 충실한 편이라 여기만 열라 뒤졌으나 원하는 것 찾지못하고.. 설마 없겠어? 하고 소스코드를 뒤져보니 보인다... ^^;

django/models/sql/constants.py

  * in
  * range
  * year, month, week_day,
  * search
  * regex

추가: 근데 허탈하게도 문서에 있더라는... <http://docs.djangoproject.com/en/1.3/ref/models/querysets/#field-lookups>

내가 찾던게 뭐냐고? in과 range다~ ㅋㅋ