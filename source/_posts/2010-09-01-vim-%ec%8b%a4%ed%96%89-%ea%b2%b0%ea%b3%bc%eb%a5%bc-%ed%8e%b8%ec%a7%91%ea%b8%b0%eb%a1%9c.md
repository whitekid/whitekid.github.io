---
id: 391
title: 'vim 실행 결과를 편집기로...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=391
permalink: /archives/391
dsq_thread_id:
  - 765968563
categories:
  - Uncategorized
tags:
  - vim
---
사용하지 않는 명령어는 잊어버리게 마련이다. 이것도 그렇다 vim에서 명령을 실행하고 그 결과를 바로 현재 버퍼에 넣고싶다면... 뭔가 있었는데.. 하고 한참 시도하다가 역시나 구글링해서 찾았다.

[code lang="plain"]  
:r !ls -al  
[/code]

이렇게 하면 ls -al 한 결과가 현재 버퍼에 추가된다. 역시나 기억 확인용 포스팅.. :!와의 차이는 다음고 같다.

  * :!{cmd} : Execute {cmd} with the shell
  * :r !{cmd} : Execute {cmd} and insert it's stardard output below the cursor or specified line.