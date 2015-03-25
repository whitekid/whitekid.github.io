---
id: 1220
title: OSX에서 bash auto competition 사용하기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1220
permalink: /archives/1220
dsq_thread_id:
  - 1132200360
categories:
  - Uncategorized
tags:
  - bash
  - MacOSX
---
MacBook을 이제 메인으로 쓰면서 Ubuntu에서 자연스럽게 되된 bash의 자동완성이 안되서 불편한 점이 있습니다. 그래서 간단히 찾아서 설정해봤군요.

먼저 [Homebrew][1]를 이용해서 bash completion 패키지를 설치하여 자동완성 지원 파일들을 설치합니다.

    $ brew install bash-completion  

이제 ~/.bash_profile에 다음 라인을 추가하여 자동완성이 되도록합니다.

    if [ -f /usr/local/etc/bash_completion ]; then  
      . /usr/local/etc/bash_completion  
    fi  

자.. 다시 쉘을 열고 "ssh "를 입력한 다음에 탭을 두번 누르면 자동완성으로 .ssh/config, .ssh/known_hosts에 있는 호스트들이 쫘악~~

끄읏~

 [1]: http://mxcl.github.com/homebrew/