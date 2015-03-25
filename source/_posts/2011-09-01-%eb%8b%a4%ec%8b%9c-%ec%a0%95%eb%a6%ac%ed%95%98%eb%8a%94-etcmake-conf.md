---
id: 740
title: 다시 정리하는 /etc/make.conf
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=740
permalink: /archives/740
dsq_thread_id:
  - 723916829
tags:
  - FreeBSD
---
    CPUTYPE= i686
    CFLAGS= -O2 -fno-strict-aliasing -pipe
    KERNCONF= YOUR_KERNEL_CONF_HERE
    NO_X= true
    WITHOUT_X11= yes

    MASTER_SITE_BACKUP?= \
        http://ftp.kr.FreeBSD.org/pub/FreeBSD/ports/distfiles/${DIST_SUBDIR}/
        http://ftp.kaist.ac.kr/FreeBSD/ports/distfiles/${DIST_SUBDIR}/

    MASTER_SITE_OVERRIDE?= ${MASTER_SITE_BACKUP}

    # sf.net 다운로드 사이트중 가장 빠른것 설정
    MASTER_SITE_SOURCEFORGE= \
        http://cdnetworks-kr-2.dl.sourceforge.net/project/%SUBDIR%/
