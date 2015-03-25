---
id: 1032
title: 'shell script: "set -e"가 뭐하는 것일까?'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1032
permalink: /archives/1032
dsq_thread_id:
  - 900747123
tags:
  - bash
---
쉘 스크립트를 보면 시작부분에 아래와 같은 문장이 있는 것을 자주 확인할 수 있다.

    set -e

그냥 무심결에 넘겨왔었는데, 오늘 찾아봤다. http://julipedia.meroh.net/2010/01/set-e-and-set-x.html

내용은 간단하다. set -e로 설정되어 있으면 쉘 스크립트가 실행될 때 모든 라인의 실행 결과를 검사해서 실패할 경우는 바로 스크립트 실행을 종료한다.

예를들어 아래의 스크립트를 보면

    #!/bin/sh
    set -e
    echo 'hello'
    false # 여기서 스크립트 실행이 끝남..
    echo 'world'

그냥 hello만 출력하고 실행을 끝낸다.

그리고 set -x는 trace로 shell이 실행하는 모든 명령을 화면에 출력한다. 디버그용으로 좋겠다.