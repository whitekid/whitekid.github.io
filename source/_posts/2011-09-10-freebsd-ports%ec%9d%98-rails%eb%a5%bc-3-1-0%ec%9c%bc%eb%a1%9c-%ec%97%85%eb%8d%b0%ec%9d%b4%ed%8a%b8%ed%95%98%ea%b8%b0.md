---
id: 747
title: FreeBSD ports의 rails를 3.1.0으로 업데이트하기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=747
permalink: /archives/747
dsq_thread_id:
  - 726643661
categories:
  - Uncategorized
tags:
  - ports
  - rails
---
FreeBSD의 ports에 들어있는 rails는 3.0.10입니다. 뭐 예내들이 조금 늦는가 했습니다. 근데 대기중인 pr에도 rails 3.1.0으로 업데이트하는 pr이 없습니다.

이미 rails guide는 3.1.0으로 가있는데.. 안되는 것도 있고... 지금와서 3.0.10에서 작업할 수 는 없잖아요.

결국 몇 시간 삽질끝에 ports를 업데이트 했습니다. 역시 쉽지 않습니다. rails는 하나의 ports가 아닌 여러 ports의 집합이죠. 그래서 www/rubygems-rails만 수정한다고 되는 것이 아니죠. actionpack, activerecord, activeresource도 모두  ports에서 업데이트 해야합니다. 게다가 하다보니깐 rubygem의 ports들이 상당히 예전 버전이 많습니다. 현재하고 호환이 안되는 버전으로요... 결국 이것까지 같이 업데이트 되고~~ ^^;

오랜만에 send-pr 할거냐구요? 글쎄요.. 아직은 모르겠습니다~ 우선 몇몇 project에 적용해서 되는지 보구요..