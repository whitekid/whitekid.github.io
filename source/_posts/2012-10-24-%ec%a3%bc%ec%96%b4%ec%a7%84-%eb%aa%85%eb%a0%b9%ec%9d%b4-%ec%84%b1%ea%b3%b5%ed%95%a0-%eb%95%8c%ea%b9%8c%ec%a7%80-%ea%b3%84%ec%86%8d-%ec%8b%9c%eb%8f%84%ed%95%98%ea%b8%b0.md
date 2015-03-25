---
id: 1016
title: '주어진 명령이 성공할 때까지 계속 시도하기...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1016
permalink: /archives/1016
dsq_thread_id:
  - 897749707
categories:
  - Uncategorized
tags:
  - bash
---

    #!/bin/bash  
    function wait_for() {  
        until $1; do  
            echo $2  
            sleep $3  
        done  
    }  

어디에 필요하냐구요?

    $ wait_for "ssh $ip echo" "waiting $ip to boot up..." 3  

이렇게 하면 서버가 부팅하고 ssh로 명령을 내릴 수 있을 때까지 대기합니다.

그렇죠 삽질이죠.. ㅎㅎ

이러다 영원히 끝나지 않을 것이 두려우신 분은 timeout 명령을 같이 쓰세요...

$ timeout 10 wait_for "ssh $ip echo" "waiting $ip to boot up..." 3  
