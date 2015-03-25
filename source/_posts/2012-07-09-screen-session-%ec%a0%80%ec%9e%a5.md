---
id: 919
title: Screen session 저장?
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=919
permalink: /archives/919
dsq_thread_id:
  - 757374462
categories:
  - Uncategorized
tags:
  - screen
---
제목이 영 이상하긴 한데요.. screen으로 작업하다보면 가끔 난감한 경우가 있습니다.

screen의 세션이 저 멀리 서버에 있어서 이 세션이 영원이 닫히지 않는다고 생각하면 별 문제 없을텐데, 이게 가끔 리부팅한다던가, 아니면 로컬에 있어서 리부팅하면 날라가는 경우.. .근데 이 세션들이 아주아주 자주 사용하는 경우 계속 세로운 세션 만드는 것 영 귀찮습니다.

저같은 경우는 개발, 테스트하면서 여러 서버들을 동시에 봐야해서, screen으로 여러개 움직이면서 작업하는데, 이거 참 날아가니깐.. 귀찮습니다.

뒤져보니 세션을 완전히 저장하고, 다시 살리는 방법은 없습니다. 하지만 가능한 방법은 screen.rc를 이용하여 그 서버에 연결되는 윈도우만 열어도 상당히 편리합니다.

screen.rc 파일을 적당히 만들고 screen -c screen.rc 로 그 세션을 시작하면 되죠..

screen.rc:  

    sessionname my-persistent-session

    screen -t 'server01&' sshpass -p <pass> ssh <server>  
    screen -t 'server01&' sshpass -p <pass> ssh <server>  

여기까지 하면 괜찮습니다. 그런데 사람의 욕심이라는게 말이죠. 위처럼하면 문제는 sshpass 명령이 종료되거나 ssh 연결이 안되면 해당 윈도우는 닫힌다는 것입니다. 이거 참 난감하죠.. 궁리해봤는데, screen 의 명령으로는 해결할 수 없고 아래처럼 ssh를 실행하는 스크립트를 만들어서 그걸로 연결하고, 해당 스크립트에서는 ssh 연결후 bash를 실행하면 됩니다.

bin/sshpass.sh:  

    #!/bin/sh  
    sshpass $1 $2 $3 $4  
    $SHELL  

그러면, screen.rc의 내용도 바뀌겠죠?

screen.rc:  

    sessionname my-persistent-session

    screen -t 'server01&' sshpass.sh -p <pass> ssh <server>  
    screen -t 'server01&' sshpass.sh -p <pass> ssh <server>  

이제 screen -s screen.rc 하면 언제나 원하는 윈도우를 연 스크린으로... ㅎㅎ

물론 더 좋은 방법 있으면 알려주삼~~