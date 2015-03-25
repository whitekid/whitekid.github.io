---
id: 1114
title: 'bash: simple job control'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1114
permalink: /archives/1114
dsq_thread_id:
  - 935503118
categories:
  - Uncategorized
tags:
  - bash
---
bash에서 child process를 돌릴 필요가 생겨서 잠깐 테스트 삼아 작성해본 background로 프로세스 돌리고, 죽이기 스크립트

    #!/bin/bash
    set -m
     
    function sig_int(){
            echo "sig_int"
     
            kill `jobs -p`
    }
     
    function child_process() {
            echo "enter child pid:$$, $1"
            sleep 5
            echo "exit someting $1"
    }
     
    # run several jobs
    for x in {1..3}; do
            #(something $x &) # parallel executation
            child_process $x & # background executation
    done
     
    trap sig_int SIGINT
     
    wait

* $$는 shell의 pid로 child process에서 같은 값이 나온다.  
* parallel executation은 child process가 아닌 현재 스크립트와 동일한 레벨로 생성된다.