---
id: 965
title: 귀찮다. ssh 접속 키 확인 무시하자구!
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=965
permalink: /archives/965
dsq_thread_id:
  - 779052419
categories:
  - Uncategorized
tags:
  - ssh
---
테스트용으로 가상 머신을 많이 사용하다 보면... 가상 머신 안에서 IP가 중복됩니다.  지금 이 순간에도 제 VMWare에는 약 20개 정도가 생성되어있고, 8개가 running 중입니다.

이런 상황에서 귀찮은 것은 ssh로 접속할 때 ssh key fingerprint가 변경되었다느니... 서버의 핑커프린트를 저장할 것이라는둥 귀찮은 질문을 막 합니다. 귀찮아서 찾아봤더니 StrictHostsKeyChecking 옵션이 있군요. 이렇게 하면 간단하게 경고 메시지를 보이면서 서버 키를 무조건 저장합니다. 귀찮게 물어보는 것이 없어졌네요  

    $ ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null 10.200.1.10  
    Warning: Permanently added '10.200.1.10' (ECDSA) to the list of known hosts.  

그리고 물론 .ssh/config 파일에 넣을 수 있습니다.  

    Host 10.200.*  
            StrictHostKeyChecking no  
    UserKnownHostsFile /dev/null  

물론 실제 환경에서는 핑커 프린트가 변경된 경우는 뭔가 이상이 있는 거니 이런거 쓰지 말기 바랍니다.