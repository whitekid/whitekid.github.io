---
id: 800
title: VMWare에서 Xen설치할때 vmnet 어쩌구저쩌구..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=800
permalink: /archives/800
dsq_thread_id:
  - 717580585
categories:
  - Uncategorized
tags:
  - vmware
---
Ubuntu에서 VMWare를 사용하고 있었습니다. 원래는 VirtualBox를 사용했었지만, 그녀석이 안의 가상머신에서는 가상화 기술(vmx or svm)을 지원해주지 않아서 말이죠..

그런데 VMware에 Xen을 설치하고 재부팅하면 네트웍 카드를 promiscuous로 허용하지 않는다고 메시지가 뜹니다. 보안때문이라나.. 간단히 아래처럼 하면 됩니다.

<pre>$ chmod a+rw /dev/vmnet*</pre>

이렇게하면 되기는 하는데, 시스템을 리부팅하면 /dev/vmnet*의 권한이 다시 재설정됩니다. ㅡㅡ 그래서 또 권한을 주고 udev에 등록을.~

<pre>$ cp -a /dev/vmnet* /lib/udev/devices</pre>

삽질 끝~