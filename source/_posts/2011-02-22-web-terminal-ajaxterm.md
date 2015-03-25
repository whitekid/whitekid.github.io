---
id: 662
title: Web Terminal ajaxterm
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=662
permalink: /archives/662
dsq_thread_id:
  - 743419887
categories:
  - Uncategorized
tags:
  - ajaxterm
  - Ubuntu
---
글쎄 오늘 네트웍이 불안하다. ssh가 계속 연결이 끊긴다. 그래 네트웍 공사중이라 이해는 하겠다만 일을 할 수가 없잖아!!!

그래서 웹에서 구현되는 터미널을 혹시나하고 뒤져보니 바로나온다. [http://en.wikipedia.org/wiki/Web-based\_SSH#Open\_source_examples][1]

우분투 패키지있는 ajaxterm 설치하고 실행하니 바로 되는구나~ 멋진 세상이다.

<pre>$ apt-get install ajaxterm
$ service start ajaxterm</pre>

<http://localhost:8022/> 로 연결하니 그냥 뜬다.. 물론 로그인해야하는것이고, 속도는 물론 느리지만 급한김에 쓸만한 놈인듯하다.

[<img class="aligncenter size-full wp-image-664" title="ajaxterm" src="http://blog.woosum.net/wp-content/uploads/2011/02/ajaxterm.png" alt="" width="647" height="543" />][2]

 [1]: http://en.wikipedia.org/wiki/Web-based_SSH#Open_source_examples
 [2]: http://blog.woosum.net/wp-content/uploads/2011/02/ajaxterm.png