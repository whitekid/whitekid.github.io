---
id: 840
title: Ubuntu 11.10에 ganglia가 설치 실패한다.
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=840
permalink: /archives/840
dsq_thread_id:
  - 714490580
categories:
  - Uncategorized
---
munin 대신 ganglia를 검토해보려고 ganglia를 설치하려는데 아래 메시지를 내뿜으며 설치가 제대로 안된다.  
`Adding group: ganglia.<br />
Adding system user: ganglia.<br />
useradd: group ganglia exists - if you want to add this user to that group, use -g.<br />
dpkg: error processing gmetad (--configure):<br />
 subprocess installed post-installation script returned error exit status 9<br />
Processing triggers for libc-bin ...<br />
ldconfig deferred processing now taking place<br />
Errors were encountered while processing:<br />
 gmetad<br />
E: Sub-process /usr/bin/dpkg returned an error code (1)`

<https://bugs.launchpad.net/ubuntu/>+source/ganglia/+bug/854866/comments/20 에 있는데로 하고 다시 설치하면 된다.  
`sudo useradd ganglia -g ganglia`

음.. 우분투 실망일세~~