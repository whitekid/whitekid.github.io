---
id: 707
title: python exception handler decorator
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=707
permalink: /archives/707
dsq_thread_id:
  - 748172464
categories:
  - Uncategorized
tags:
  - decorator
  - pyt
---
쩝.. 내가봐도 참 난해한 코드 ㅋㅋㅋ  
[code lang="python"]  
class MyException(Exception): pass  
class MyException1(Exception): pass  
class MyException2(Exception): pass

def exception\_catch(exceptions, exception\_handler):  
def wrap(func):  
def new_func(\*args, \**kwargs):  
try:  
return func(\*args, \**kwargs)  
except exceptions, e:  
return exception_handler(e, \*args, \**kwargs)  
return new_func  
return wrap

def error_handler(\*args, \**kwargs):  
print 'handle error'

def my_function():  
pass

@exception\_catch((MyException, MyException1), error\_handler)  
def my\_raise\_function():  
raise MyException, 'hello'

@exception\_catch((MyException, MyException1), error\_handler)  
def my\_raise\_function_1():  
raise MyException2, 'unexpected error'

@exception\_catch(MyException, error\_handler)  
def my\_raise\_function_2():  
raise MyException, 'unexpected error'

my_function()  
my\_raise\_function()  
my\_raise\_function_2()  
[/code]