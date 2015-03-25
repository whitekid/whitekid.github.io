---
id: 977
title: vim modeline
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=977
permalink: /archives/977
dsq_thread_id:
  - 858576896
categories:
  - Uncategorized
tags:
  - vim
---
텍스트 파일을 편집하다 보면 아래와 비슷한 형식의 내용을 접한 적이 있을 것이다.

이것은 modeline이라 부르는 것으로 vimrc의 설정이 vim을 실행하면 바로 적용되는 것인 반면에, modeline은 파일에 적은 것으로 알 수 있듯이 파일 단위로 설정을 적용하는 것이다.

물론 filetype plugin을 통해서 파일 타입별로 셋팅을 맞출 수 있으나, 다른 사람과의 공동작업을 위해서는 다른 사람도 같은 설정 하에서 작업하기 위해서는 이 방법이 편한다.

    # vim: ts=4 sw=4 tw=80 nu  

python이라면 일반적으로 아래와 같이 modeline을 적을 것이다.

    # vim: expandtab ts=4 sw=4 tw=80 nu  

만일 C 소스라면 modeline은 아래와 같이 적을 수 있다.

    /* vim: expandtab ts=4 sw=4 tw=80 nu */  

그리고 이 설정은 modeline 옵션이 설정되어 있어야 한다. 따라서 안된다면 set modeline 으로 설정하거나 vimrc에 넣으면 된다. Ubuntu에서는 기본으로 꺼져있다.

ps. 이 글을 적는 이유는 이렇게 사용한다는 것은 알겠는데, 이게 modeline이라는 사실을 잃어버려 항상 검색할 때 키워드를 못찾아서 그런다. ㅎㅎ