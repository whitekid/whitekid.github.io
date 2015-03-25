---
id: 1489
title: -Wunused-command-line-argument-hard-error-in-future 오류
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1489
permalink: /archives/1489
dsq_thread_id:
  - 2595002490
tags:
  - pip
  - Xcode
---
OSX에서 brew, pip, gem 등으로 패키지 설치할 때 위와 같은 컴파일 오류가 나는 경우가 있다. 이건 Xcode가 업데이트 되면서 오류를 만들어 내는 것으로 간단히 해당 옵션을 무시하면 된다.

    $ export ARCHFLAGS="-Wno-error=unused-command-line-argument-hard-error-in-future"
    $ pip install foobar

  * https://langui.sh/2014/03/10/wunused-command-line-argument-hard-error-in-future-is-a-harsh-mistress/

sudo도 실행할 경우는 -E 옵션을 준다.