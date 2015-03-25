---
id: 707
title: python exception handler decorator
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=707
permalink: /archives/707
dsq_thread_id:
  - 748172464
tags:
  - decorator
  - pyt
---
쩝.. 내가봐도 참 난해한 코드 ㅋㅋㅋ

    class MyException(Exception): pass
    class MyException1(Exception): pass
    class MyException2(Exception): pass


    def exception_catch(exceptions, exception_handler):
        def wrap(func):
            def new_func(*args, **kwargs):
                try:
                    return func(*args, **kwargs)
                except exceptions, e:
                    return exception_handler(e, *args, **kwargs)
            return new_func
        return wrap

    def error_handler(*args, **kwargs):
        print 'handle error'

    def my_function():
        pass

    @exception_catch((MyException, MyException1), error_handler)
    def my_raise_function():
        raise MyException, 'hello'

    @exception_catch((MyException, MyException1), error_handler)
    def my_raise_function_1():
        raise MyException2, 'unexpected error'

    @exception_catch(MyException, error_handler)
    def my_raise_function_2():
        raise MyException, 'unexpected error'


    my_function()
    my_raise_function()
    my_raise_function_2()