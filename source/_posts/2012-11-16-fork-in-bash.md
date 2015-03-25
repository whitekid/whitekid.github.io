---
id: 1108
title: fork(?) in bash
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1108
permalink: /archives/1108
dsq_thread_id:
  - 930828049
tags:
  - bash
---
shell script에서 fork와 비슷한 같은 작업을 하는 것은 간단하다. 함수를 호출뒤에 &를 넣어주면 되니깐... 이게 정확하게 fork와 같은 역할을 하는 지는 잘 모르겠지만, 비슷한 역할을 한다.

    #!/bin/bash
    function foo() {
        echo "function foo $1"
        sleep 5
        echo "exiting foo $1"
    }

    for x in {1..5}; do
        foo $x &
    done

    echo 'waiting...'
    wait

foo 함수를 5개 호출하면서 child process로 호출하고, parent에서는 child process가 종료할 때 까지 기다린다. 다음은 실행 결과

    $ bash fork.sh
    function foo 1
    function foo 2
    waiting...
    function foo 3
    function foo 5
    function foo 4
    exiting foo 1
    exiting foo 4
    exiting foo 3
    exiting foo 5
    exiting foo 2

이걸 어디에 사용하냐구요? 그래요 snapshot를 뜨는데, 하나하나 하기가 너무 느려서 백그라운드로 돌릴려구요.. 잘 될려나..