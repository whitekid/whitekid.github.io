---
id: 656
title: 'Python 함수에서 static 변수처럼 사용하기...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=656
permalink: /archives/656
dsq_thread_id:
  - 714768598
categories:
  - Uncategorized
tags:
  - python
---
class에서는 static 변수가 지원됩니다. 그런데 함수에서 가끔 static이 필요할 때가 있습니다.

static을 사용하지 않는다면, global을 써야하는데, global을 사용한다는 것은 항상 꺼려지는 것이기에 static이 권장되죠. 하지만 python은 함수에 static을 지원하지 않고, 비슷하게 이를 흉내낼 수 있습니다.

_static을 잘 보시면 됩니다. 자세한 내용은 생략 ㅋㅋ

[code lang="python"]  
def get\_connection(\_static = {}):  
if 'conn' not in _static:  
conn = MySQLdb.connect(host=DB\_HOST, user=DB\_USER, passwd=DB\_PASS, db=DB\_NAME, use_unicode=True, charset='utf8&')  
_static['conn'] = conn  
return _static['conn']

def get_cursor():  
return get_connection().cursor()  
[/code]