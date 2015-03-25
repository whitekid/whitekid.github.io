---
id: 688
title: 파라미터가 있는 decorator
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=688
permalink: /archives/688
dsq_thread_id:
  - 748174145
categories:
  - Uncategorized
tags:
  - decorator
  - python
---
Python의 Decorator 기능은 한마디로 멋지다. 잘 쓰면 유용하지... 근데 작성하면 항상 헷갈린단 말이지...

decorator에 파라미터를 넘겨주고자 하는 경우에도 상당히 헷갈린다. 파라미터가 없는 것하고 형태가 아주 또 달라지거든.. 간단히 기억하자 파라미터가 있는 데코레이터는 wrapp 함수를 한번 더 두어야한다는 것..

[code lang="python"]  
def prefix_decorator(func):  
def new_func(\*args, \**kwargs):  
return 'prefix: ' + func(\*args, \**kwargs)  
return new_func

def my_decorator(str):  
def wrap(func):  
def new_func(\*args, \**kwargs):  
return func(\*args, \**kwargs) + ' :' + str  
return new_func

return wrap

@prefix_decorator  
@my_decorator('added by decorator')  
def test():  
return "I'm test"

print test()  
[/code]

실행 결과는 아래와 같음..

[code lang="plain"]  
prefix: I'm test :added by decorator  
[/code]