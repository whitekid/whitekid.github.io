---
id: 119
title: msbuild를 이용해서 컴파일할때 map 정보넣기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=119
permalink: /archives/119
the_sidebar:
  - 
sidebar_layout:
  - 
content_sidebar:
  - 
colorscheme:
  - 
full_width_widget:
  - 
hide_bottom_sidebars:
  - 
featureboxes:
  - 
carousel_items:
  - 
carousel_mode:
  - 
carousel_ngen_gallery:
  - 
featuretitle:
  - 
featuretext:
  - 
featuremedia:
  - 
dsq_thread_id:
  - 740730073
categories:
  - Uncategorized
tags:
  - Delphi
  - JclDebug
  - StackTrace
---
JclDebug Expert를 이용해서 실행파일에다가 map파일 정보를 넣는건 쉽습니다. 그냥 메뉴에서 선택해주면 알아서 하니깐요.

하지만 우리 프로젝트에서는 msbuild를 이용해서 빌드를 하는데, 이걸로하면 JclDebug Expoert가 동작하지 않아서 map파일이 들어가지 않습니다. 흠..

이걸 어떻게 하나고 뒤져보니 [Jcl에 이미 프로그램이 들어가있습니다][1]. 하지만 소스만 들어가 있이니 컴파일해서 적용하면 되겠지요.

  1. msbuild MyProg.dproj /t:build /p:Configuration=Release /p:DCC\_Define=RELEASE /p:DCC\_MapFile=3  
    빌드할때 맵파일을 생성합니다.
  2. MakeJclDebug -E MyProg.map  
    컴파일이 끈나면 map파일을 압축해서 프로그램 바이너리에 집어넣습니다.

이렇게 하고 JclDebug에서 제공하는 ExceptionDialog가 실행되면 자동으로 바이너리의 map파일을 이용해서 StackTrace를 출력해줍니다.

 [1]: http://jedi.jcl.free-usenet.eu/Build-delphi-2009-project-with-MSBUILD-command-mode-!_T25464074_S1