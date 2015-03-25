---
id: 438
title: psycopg2 쿼리 결과를 유니코드로 받기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=438
permalink: /archives/438
dsq_thread_id:
  - 717522170
categories:
  - Uncategorized
tags:
  - psycopg
  - python
---
psycopg2에서 쿼리의 결과는 client_encoding에 따른 그 형태 그대로 옮니다. 이 경우 인코딩을 처리하면서 해야하면 상당한 불편함이 따르지요.

무식하게 하나하나 unicode(row[0], 'utf8&')로 인코딩해가면서 하고 있었습니다. 진짜 무식하게..

근데 설마 있겠지.. 하고 찾아봤더니 역시나 있군요.

[code lang="python"]  
import psycopg2  
from lib import *

psycopg2.extensions.register_type(psycopg2.extensions.UNICODE)  
cursor = get_cursor()  
cursor.execute('select subject from messages order by id desc limit 1&')

for row in cursor:  
print type(row[0])  
[/code]  
실행 결과는...  
[code lang="plain"]  
$ python test.py  
<type 'unicode'>  
[/code]

역시.. 사람은 공부해야 손발이 고생하지 않는다.