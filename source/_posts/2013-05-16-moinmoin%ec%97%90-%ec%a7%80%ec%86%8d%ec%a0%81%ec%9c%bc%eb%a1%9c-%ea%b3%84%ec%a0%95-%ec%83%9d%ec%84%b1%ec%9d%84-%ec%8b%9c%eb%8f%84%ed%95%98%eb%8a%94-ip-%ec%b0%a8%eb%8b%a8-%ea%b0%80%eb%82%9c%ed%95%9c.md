---
id: 1266
title: 'moinmoin에 지속적으로 계정 생성을 시도하는 ip 차단: 가난한 자의 ddos 방어..'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1266
permalink: /archives/1266
dsq_thread_id:
  - 1291590316
tags:
  - apache
  - bash
---
본 서버에 같이 돌아가는 서비스 중에 하나가 moinmoin 입니다. 그냥 혼자 이것저것 정리한 내용들을 두서없이 적는데...

어떤 녀석들이 여기에다가 지속적으로 새로운 계정 생성을 시도하면서 스팸 짓을 합니다. 녜.. 여기 서버는 가상서버에다가 메모리도 1기가로 아주 작아서 이런 요청이 계속 들어오면 apache 프로세스그 많아서서 서버가 엄청 느려집니다.

그래서 해당 ip를 그냥 .htaccess에서 접근차단 하기로 했습니다. 아주 간단하게.~~

{% gist 5589530 %}

이 스크립트를 그냥 crontab에서 돌려 놓으세요~

물론 다른 나이스한 방법이 있겠지만.. 우선 귀찮아서... ^^;