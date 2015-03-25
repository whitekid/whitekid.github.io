---
id: 740
title: 다시 정리하는 /etc/make.conf
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=740
permalink: /archives/740
dsq_thread_id:
  - 723916829
categories:
  - Uncategorized
tags:
  - FreeBSD
---
[code]  
CPUTYPE= i686  
CFLAGS= -O2 -fno-strict-aliasing -pipe  
KERNCONF= YOUR\_KERNEL\_CONF_HERE  
NO_X= true  
WITHOUT_X11= yes

MASTER\_SITE\_BACKUP?= \

<http://ftp.kr.FreeBSD.org/pub/FreeBSD/ports/distfiles/>${DIST_SUBDIR}/

<http://ftp.kaist.ac.kr/FreeBSD/ports/distfiles/>${DIST_SUBDIR}/

MASTER\_SITE\_OVERRIDE?= ${MASTER\_SITE\_BACKUP}

\# sf.net 다운로드 사이트중 가장 빠른것 설정  
MASTER\_SITE\_SOURCEFORGE= \

<http://cdnetworks-kr-2.dl.sourceforge.net/project/%SUBDIR%/>

[/code]