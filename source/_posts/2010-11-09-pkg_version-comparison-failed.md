---
id: 595
title: 'pkg_version: Comparison failed'
author: whitekid
layout: post
guid: http://www.woosum.net/?p=595
permalink: /archives/595
dsq_thread_id:
  - 752694722
categories:
  - Uncategorized
tags:
  - FreeBSD
  - portsnap
---
ports에 업데이트할 것이 있는지 확인하려고 pkg_version -vIL= 을 실행했다. 그런데 갑자기 Comparison failed라는 메시지가 나오면서 아무런 것도 안한다.

수동으로 portsnap fetch update 하면 모든 포트가 없데이트 되었다고 나온다. 뭔가 잘못된거다. 여기저기 뭔가하고 뒤저보다가... 무심결에 ls -l /usr/ports 하는 순간..

허걱 /usr/ports/INDEX 파일의 사이즈가 0이다. 뭐 이건 인덱스 다시 빌드하면 되는 것이고.. 또 뒤져보니 /var/db/portsnap 파일의 일부가 사라졌다. 이건 뭣인가? 그래 언젠가부터 ports의 nfs 서버가 이상하긴 했지, 뭐 그녀석 이제 생을 마감할 때도 되었고, 그냥 ports나 서비스 해라 하고 놔두고 있었는데, 그녀석이 하드웨어적인 이유로 재부팅 되면서 몇몇 파일을 날려버렸던 거다.

원인은 파악됐고... 해결은 간단하다 /var/db/portsnap 지워버리고 portsnap fetch extract 끄읏...

근데 그 에러난 머신은 어떻하냐구? 말했잖아! 조용히 생을 마감하도록 호스피스 병동에 격리시킬 생각이지요. 이렇게 가끔 ports nfs 서비스 하다가... 그게 잦아지면 그냥 생을 마감시키려해... 그래 그녀석 서버로서 지난 8년간의 새월은 참으로 오랜 세월이었지???

너도 젊었을 때는 스펙으론 꽤나 날리던 녀석이었는데, 물론 대형 형님들은 제외하고 말이다... 그리고 너와 참 많은 일을 했었는데, 아쉽다.... 그래 그래... 나도 그 정때문에 널 아직까지 버리지 못하고 있는걸거야...