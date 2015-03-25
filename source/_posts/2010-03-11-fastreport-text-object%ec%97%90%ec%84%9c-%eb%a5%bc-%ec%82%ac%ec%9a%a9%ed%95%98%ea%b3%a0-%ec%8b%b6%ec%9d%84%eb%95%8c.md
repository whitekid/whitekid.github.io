---
id: 56
title: 'FastReport Text Object에서 []를 사용하고 싶을때'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=56
permalink: /archives/56
dsq_thread_id:
  - 759657356
categories:
  - Uncategorized
tags:
  - Delphi
  - FastReport
---
FastReport에서 TextObject에서 데이터를 표시할때 [table\_name."column\_name"] 이렇게 사용합니다.  
그런데 [, ] 를 표시하고 싶을때 예를 들면 "[안녕하세요]"라고 표시하고 싶을땐 안됩니다. 뭔가 escape하는 방법이 없을까하고 열심히 해봤지만 안되드라구요. 그래서 문서좀 뒤져봤더니 두가지 방법이 있습니다.

### Expression을 꺼버리기

TextObject 프러퍼티의 AllowExpression을 False로 설정합니다. 그러면 입력한 그대로 출력됩니다.

그런데 문제는 DB의 데이터를 표시할때 Expression을 꺼버리면 안되죠. 그래서 아래 방법으로 합니다.

### Expression Deliliter를 바꾸기

역시 프러퍼티에 보면 ExpressionDelimiter가 있습니다 . 이걸 다른것으로 바꾸면 됩니다. "<,>" 처럼 설정했다면 [table\_name."column\_name"] 대신 <table\_name."column\_name">게 하면 됩니다. 이제 이 TextObject는 [ ] 를 표시할 수 있습니다.