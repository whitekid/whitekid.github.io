---
id: 790
title: gnu screen에서 스크린 이동
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=790
permalink: /archives/790
dsq_thread_id:
  - 750443901
tags:
  - screen
---
음.. 말로 설명하기 좀 거시기 한데.. screen에서 계속 윈도우를 만들면 비어있는 윈도우중 가장 빠른 번호의 윈도으로 만들어 집니다.

윈도우가 0, 1, 4, 5, 7 이렇게 있다면 새로운 윈도우를 만들면 3으로 할당되는 것이죠.

그런데 가끔 이 윈도우를 맘대로 이동하고 싶을 때가 있습니다. 비슷한 작업을 하는 윈도우는 바로 옆에 놓고 싶은데요.. 그럴 경우는

    ^a:number: 1

이렇게 합면 됩니다. 저거 보고 좀 헷갈리긴 하죠.

  1. ^a: 를 입력하면 screen prompt가 나옵니다.
  2. number: 1 을 입력합니다.

http://lists.gnu.org/archive/html/screen-users/2007-11/msg00002.html

즐거운 screen 생활 하시길~