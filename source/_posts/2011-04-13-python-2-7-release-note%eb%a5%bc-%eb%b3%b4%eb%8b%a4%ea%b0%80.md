---
id: 710
title: Python 2.7 Release Note를 보다가..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=710
permalink: /archives/710
dsq_thread_id:
  - 730463118
categories:
  - Uncategorized
tags:
  - python
---
<http://docs.python.org/dev/whatsnew/2.7.html#pep-372-adding-an-ordered-dictionary-to-collections>

순서가 보장되는 딕셔너리.. 그래 이거 필요했다. 특히 json으로 넒길때 아무리 순서가 없다지만. .그래도 순서 있는게 좋지 않겠어?

<http://docs.python.org/dev/whatsnew/2.7.html#pep-378-format-specifier-for-thousands-separator>

format()이란것을 통해서 해야된다는 것이 불편하긴 하지만... 그래도 locale 모듈을 통해서 한다는 것에 비하면, 천지차이라고나 할까?

<http://docs.python.org/dev/whatsnew/2.7.html#pep-3106-dictionary-views>

그래 이것도 맞는 것 같다... key를 iteration하면서 그 리스트를 수정하는 경우.. 뭔가 애매한 부분이 있었었지...

아래건... 실수할 가능성이 많아진 문법인듯..

[code lang="python"]  
>>> {1,2,3,4,5}  
set([1, 2, 3, 4, 5])  
>>> set() # empty set  
set([])  
>>> {} # empty dict  
{}  
[/code]

<http://docs.python.org/dev/library/io.html#module-io>

cStringIO 보단 그냥 io 써야겠다.. C로 작성되어 2~20배 빨라졌다고 함... 근데 편차가 왜이리 큰겨?

<http://docs.python.org/dev/library/shutil.html#shutil.make_archive>

간단한 압축엔 유용하겠는걸...</pre>