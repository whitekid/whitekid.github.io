---
id: 464
title: crontab에서 portsnap 수행하기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=464
permalink: /archives/464
dsq_thread_id:
  - 717695951
tags:
  - FreeBSD
  - portsnap
---
    0     3     *     *     *     root    portsnap cron update && pkg_version -vIL=

자 이렇게하면 메일 새벽 3시에 ports의 snapshot을 받아서 업데이트 한다.

  * -I : INDEX만 업데이트하고 나머지 포트트리는 건드리지 않는다. ports 빌드중에 portsnap으로 업데이트하면 빌드오류가 발생할 가능성이 높으므로 그때 이 옵션이 필요하다. 하지만 새벽 3시에 포트를 빌드하는 머신은 무슨 머신일까???
  * cron: 1~3600초를 random으로 sleep한 다음 수행한다. 이는 서버의 부하를 분산해주려는 목적임
  * fetch: man page에 따르면 crontab에 적용하려면 fetch는 사용하지 마란다. cron 명령으로 내부적으로 호출된다. 직접  portsnap을 실행할 때만 사용한다.
  * pkg_version: 업데이트 한 후.. ports와 버전이 다른 것 리포팅 한다. 뭐 실험적인 서버에서는 모두 업데이트해 최신 버전을 유지하는 것도 좋지만 portaudit에서 보안 오류가 난 것만 업데이트 해주는 것이 정신 건강에 좋을 수 있다.