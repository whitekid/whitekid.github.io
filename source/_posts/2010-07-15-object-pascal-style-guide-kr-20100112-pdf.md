---
id: 279
title: Object Pascal Style Guide kr 20100112.pdf
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=279
permalink: /archives/279
dsq_thread_id:
  - 737150102
tags:
  - Delphi
---
[Object+Pascal+Style+Guide\_kr\_20100112.pdf][1].

  * 그리고 begin 문은  항상 새로운 줄에서 시작합니다.
```
    if IsTrueValue then
    begin
      DoSomething;
    end
    else
    begin
      DoSomethingElse;
    end;
```
이런 경우 end, else 줄이 너무 낭비 같은 느낌이 드는데.. 쩝.. 8.2.3 if 문에선 길게 사용하라는군.. 아님 else를 붙이던지..

  * 메소드는 알파벳 순서에 따라 정렬합니다.
  * If 문은 적어도 두 줄에 표현됩니다.

 [1]: http://bit.ly/9n00Pz