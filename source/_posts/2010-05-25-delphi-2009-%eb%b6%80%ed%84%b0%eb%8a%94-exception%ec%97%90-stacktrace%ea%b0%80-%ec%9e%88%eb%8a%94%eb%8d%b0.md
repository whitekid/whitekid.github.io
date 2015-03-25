---
id: 107
title: Delphi 2009 부터는 Exception에 StackTrace가 있는데?
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=107
permalink: /archives/107
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
  - 720641150
categories:
  - Uncategorized
tags:
  - Delphi
  - StackTrace
---
StackTrace를 확인하기 위해서 JclDebug를 사용하고 있었는데(2007 버전부터), 혹시나하고 [StackOverflow][1]에서 [찾아봤더니][2] Delphi 2009 버전부터는 [Exception에 StackTrace 정보가 들어가 있다][3]{.broken_link}.

시간날 때 공부해야겠군요.

  * [Working with Delphi’s new Exception.StackTrace][4]

추가: 좀 봤는데, 실제로 StackTrace를 보여주는 게 아니라 그를 위한 Framework입니다. 그냥 [JclDebug][5]로 하세용.

 [1]: http://stackoverflow.com/
 [2]: http://stackoverflow.com/search?q=%2Bdelphi+%2Btrace
 [3]: http://blog.eurekalog.com/new-exception-class-in-delphi-2009-and-above/
 [4]: http:// "http://blog.gurock.com/postings/working-with-delphis-new-exception-stacktrace/730/"
 [5]: http://blog.woosum.net/2010/06/msbuild%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%B4%EC%84%9C-%EC%BB%B4%ED%8C%8C%EC%9D%BC%ED%95%A0%EB%95%8C-map-%EC%A0%95%EB%B3%B4%EB%84%A3%EA%B8%B0/