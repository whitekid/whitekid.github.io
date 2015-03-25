---
id: 529
title: 'TSQLConnection.SQLHourGlass란 놈은 참...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=529
permalink: /archives/529
dsq_thread_id:
  - 740660173
categories:
  - Uncategorized
tags:
  - dbExpress
  - Delphi
  - TSQLConnection
---
TSQLConnection.SQLHourGlass 가 있다. 그래 이걸 보고 상상을 하면 TSQLConnection을 이용해서 뭔가 서버에 쿼리를 주고 받고 하는 동안에  커서를 HourGlass로 표시해주는 기능일 것 같다.

그런데 이런 예상과는 완벽하게 다르게 서버에 연결할 때만 HourGlass를 표시해준다. 쩝... 아 이 사람들아!!! 연결할 때 뿐만 아니라 쿼리할때도 더 시간이 걸린다고!!!! 글쎄 기억이 가물가물해서 그런지 모르지만, TDatabase 같은 것에선 지원했던것 같다.

그래서 쿼리 던질때 마다 SQLHourGlass를 표시하려면 어떻게 하나고?

글쎄다.. 아직은 모르겠다. 나중에 해결하면 알려주마.^^;