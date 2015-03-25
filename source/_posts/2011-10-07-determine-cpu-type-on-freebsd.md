---
id: 759
title: Determine CPU Type on FreeBSD
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=759
permalink: /archives/759
dsq_thread_id:
  - 717145465
categories:
  - Uncategorized
---
/etc/make.conf에 CPUTYPE을 설정하려고 CPUTYPE을 정확하게 알고싶다면 sysutils/x86info를 사용하면 된다.

<pre># x86info
x86info v1.27.  Dave Jones 2001-2010
Feedback to &lt;<a href="mailto:davej@redhat.com">davej@redhat.com</a>&gt;.

Found 2 CPUs
--------------------------------------------------------------------------
CPU #1
EFamily: 1 EModel: 0 Family: 15 Model: 10 Stepping: 0
CPU Model: Unknown CPU
Processor name string: AMD Phenom(tm) II X6 1055T Processor
Monitor/Mwait: min/max line size 0/0
Address Size: 48 bits virtual, 48 bits physical
The physical package has 12 of 8 possible cores implemented.
--------------------------------------------------------------------------
CPU #2
EFamily: 1 EModel: 0 Family: 15 Model: 10 Stepping: 0
CPU Model: Unknown CPU
Processor name string: AMD Phenom(tm) II X6 1055T Processor
Monitor/Mwait: min/max line size 0/0
Address Size: 48 bits virtual, 48 bits physical
The physical package has 12 of 8 possible cores implemented.
--------------------------------------------------------------------------
 running at an estimated 2.85GHz</pre>

그냥 athelon64로 설정했음.