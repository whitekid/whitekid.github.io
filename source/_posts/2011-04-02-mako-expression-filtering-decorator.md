---
id: 696
title: mako expression filtering, decorator
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=696
permalink: /archives/696
dsq_thread_id:
  - 752292855
categories:
  - Uncategorized
tags:
  - mako
  - python
---
## <http://www.makotemplates.org/docs/filtering.html#expression-filtering>

  * <tt>u</tt> : URL escaping, provided by <tt>urllib.quote_plus(string.encode('utf-8'))</tt>
  * <tt>h</tt> : HTML escaping, provided by <tt>markupsafe.escape(string)</tt> (new as of 0.3.4 - prior versions use <tt>cgi.escape(string, True)</tt>)
  * <tt>x</tt> : XML escaping
  * <tt>trim</tt> : whitespace trimming, provided by <tt>string.strip()</tt>
  * <tt>entity</tt> : produces HTML entity references for applicable strings, derived from <tt>htmlentitydefs</tt>
  * <tt>unicode</tt> (<tt>str</tt> on Python 3): produces a Python unicode string (this function is applied by default).
  * <tt>decode.<some encoding></tt> : decode input into a Python unicode with the specified encoding
  * <tt>n</tt> : disable all default filtering; only filters specified in the local expression tag will be applied.

필터는 그냥 스트링 인자를 하나 받는 함수이므로 작성하기도 편하다.

그런데 아쉽게도 필더에 파라미터가 들어가지 않는구나..

## <http://www.makotemplates.org/docs/filtering.html#decorating>

mako의 함수에 python의 decorator처럼 사용할 수 있다. 멋진데?