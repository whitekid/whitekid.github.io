---
id: 696
title: mako expression filtering, decorator
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=696
permalink: /archives/696
dsq_thread_id:
  - 752292855
tags:
  - mako
  - python
---
## http://www.makotemplates.org/docs/filtering.html#expression-filtering

  * u : URL escaping, provided by urllib.quote_plus(string.encode('utf-8'))
  * h : HTML escaping, provided by markupsafe.escape(string) (new as of 0.3.4 - prior versions use cgi.escape(string, True))
  * x : XML escaping
  * trim : whitespace trimming, provided by string.strip()
  * entity : produces HTML entity references for applicable strings, derived from htmlentitydefs
  * unicode (str on Python 3): produces a Python unicode string (this function is applied by default).
  * decode.<some encoding> : decode input into a Python unicode with the specified encoding
  * n : disable all default filtering; only filters specified in the local expression tag will be applied.

필터는 그냥 스트링 인자를 하나 받는 함수이므로 작성하기도 편하다.

그런데 아쉽게도 필더에 파라미터가 들어가지 않는구나..

## http://www.makotemplates.org/docs/filtering.html#decorating

mako의 함수에 python의 decorator처럼 사용할 수 있다. 멋진데?