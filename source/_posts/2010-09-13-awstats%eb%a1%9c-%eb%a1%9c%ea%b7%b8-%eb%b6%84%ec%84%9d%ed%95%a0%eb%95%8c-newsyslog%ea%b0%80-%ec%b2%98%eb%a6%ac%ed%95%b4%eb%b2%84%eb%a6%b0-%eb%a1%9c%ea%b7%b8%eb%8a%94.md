---
id: 431
title: awstats로 로그 분석할때 newsyslog가 처리해버린 로그는???
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=431
permalink: /archives/431
dsq_thread_id:
  - 733536759
tags:
  - awstats
---
awstats로 로그를 가끔 보는데, 갑자기 노파심이 듭니다.
newsyslog로 로그 파일을 관리하는데, awstats가 로데이트 된 로그파일을 처리할 수 없기 때문에, newsyslog 실행되고 다름 로그파일로 넘어갔을때, 이전 로그는 분석이 안되는 걸 어떻게 하나라는 완전 씰때없는 생각이 들었습니다. 그래봤자 이 사이트에 누가 들어온다고 말이지요.. ^^;

근데 awstats가 훌륭하게도 로그 파일에 command pipe를 지정할 수 있게 되어있네요. 그래서 다음처럼 삽질을 했더니 잘됩니다. ㅋㅋ

    LogFile="(bzcat /var/log/httpd-access.log.0.bz2 ; cat /var/log/httpd-access.log)|"