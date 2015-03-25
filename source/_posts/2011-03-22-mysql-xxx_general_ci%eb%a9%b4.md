---
id: 680
title: 'MySQL XXX_general_ci면...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=680
permalink: /archives/680
dsq_thread_id:
  - 716454217
tags:
  - MySQL
---
MySQL에서 LIKE로 검색하는데 이놈이 대소문자를 구별하지 않고 검색하는거다. 이거 참 이놈은 ILIKE가 기본값인가? 하고 넘어갔었는데 오늘은 = 로 검색하는데도 대소문자를 구별하지 않고 검색하는거다... 나로썬 황당~

주위 분들게 물어봤더니 euckr\_general\_ci로 되어있으면 기본으로 대소문자를 구별하지 않는다고 한다. _cs면 대소문자를 구분한다나... ㅡㅡ

뭐 이런 일이... 하여간 이상한 MySQL이다.~

그럼 Case Sentive 검색을 하려면 어떻게 하느냐? euckr\_general\_ci는 Case Insentive로 되기때문에 \_cs로 해야하는데 euckr\_general\_cs가 없다. 대신 euckr\_bin이 있으니 그걸로 하면 된다.

    select field from table where field COLLATE euckr_bin = 'test string'