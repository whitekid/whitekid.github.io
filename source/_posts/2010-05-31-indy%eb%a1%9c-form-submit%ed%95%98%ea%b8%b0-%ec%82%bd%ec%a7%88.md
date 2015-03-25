---
id: 110
title: Indy로 form submit하기 삽질
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=110
permalink: /archives/110
dsq_thread_id:
  - 737490477
categories:
  - Uncategorized
tags:
  - Delphi
  - drupal
  - Indy
---
  * TIdMultipartFormDataStream를 이용하면 될 것 같아서 열심히 했다.
  * 근데 UTF8 인코딩이 필요하다. AddFormField로 추가하면 될 것 같았다. AddObject도 안된다.
  * TIdMultipartFormDataStream는 유니코드를 전혀 지원하지 않는다.
  * 그냥 TStrings로 했다. 잘된다.. ㅡㅜ

Drupal에서 삽질

  * WebForms에서 만들 폼에다가 넣는 중이있다.
  * 잘 넣는데 가끔 폼에 적은 내용이 전달이 되지 않는다. 흠... 한참 삽질하다 다시 보니 WebForms의 필드 타입이 textfield인 곳에 여러줄 넣으면 안된다. 아마 여러줄에 이상한 문자가 있었나?.. 하여간 가끔안되다가 되다가.
  * 여러줄 필요한 곳의 타입을 textarea로 변경하니 잘 된다.