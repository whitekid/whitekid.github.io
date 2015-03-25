---
id: 434
title: sysutils/screen 기본 사용법
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=434
permalink: /archives/434
dsq_thread_id:
  - 771441637
categories:
  - Uncategorized
tags:
  - screen
---
[Screen][1]을 이용하면, 로그 아웃 이후에도 그 화면을 유지하고 나중에 동일한 환경으로 복원한다. 그리고 screen안에서 프로그램이 실행중이라면 그 프로그램이 실행중이므로 장시간 동작이 필요한 겨우 터미널을 닫고 다른데서(혹은 같은데서) 다시 작업을 이어가기 위함이다. 혹자는 텍스트 버전 vnc라고도 한다네...

> **screen 실행 옵션**  
> screen -S [세션 이름]  
> screen -list  
> screen -r [세션 이름]: resume session  
> screen -x: multi display mode

> **screen 명령어**  
> Ctrl+a,c 새 세션 열기  
> Ctrl+a,a 다음 세션으로 이동  
> Ctrl+a,k 스크린 닫기  
> Ctrl+a,d Detatch  
> Ctrl+a,w 스크린 리스트  
> Ctrl+0~9: 지정 창으로 이동  
> Ctrl+x  
> Ctrl+a,S 화면 수평 분할  
> Ctrl+a,Tab 화면 이동  
> Ctrl+a,X 현재 화면 닫기

역시나 글을 적으면서 기억하는거다.

 [1]: http://www.freshports.org/sysutils/screen