---
id: 835
title: CentOS bootstraping in Ubuntu
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=835
permalink: /archives/835
dsq_thread_id:
  - 727216333
categories:
  - Uncategorized
tags:
  - CentOS
  - Ubuntu
---
[code lang="bash"]  
$ apt-get install yum rpm python-m2crypto  
$ mkdir -p /tmp/centos/var/lib/rpm  
$ rpm -root /tmp/centos -initdb  
[/code]  
$HOME/.rpmdb 가 만들어지는데 그거는 마지막 삭제한다.

rpmfind.net에서 원하는 버전의 rpm 찾아서 다운로드  
[code lang="bash"]  
$ wget [ftp://195.220.108.108/linux/centos/5.7/os/x86\_64/CentOS/centos-release-5-7.el5.centos.x86\_64.rpm][1]  
$ rpm -ivh -force-debian -nodeps -root /tmp/centos centos-release-5-7.el5.centos.x86_64.rpm  
$ ln -s /tmp/centos/etc/pki /etc/pki  
$ yum -installroot /tmp/centos install yum  
$ mount -t proc foo /tmp/centos/proc  
$ mount -t sysfs foo /tmp/centos/sys  
[/code]

[code lang="bash"]  
$ chroot /tmp/centos /bin/bash -login  
$ rm -rf /home/choe/.rpmdb # 위에서 initdb한것 지우기  
$ rpm -initdb  
$ echo nameserver 168.126.63.1 > /etc/resolv.conf  
$ yum install yum  
$ yum install vim-minimal less  
[/code]

참고: <http://www.lucas-nussbaum.net/blog/?p=385>

 [1]: ftp://195.220.108.108/linux/centos/5.7/os/x86_64/CentOS/centos-release-5-7.el5.centos.x86_64.rpm