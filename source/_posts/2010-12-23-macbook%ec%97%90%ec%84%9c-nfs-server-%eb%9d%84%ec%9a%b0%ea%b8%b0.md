---
id: 640
title: Macbook에서 nfs server 띄우기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=640
permalink: /archives/640
dsq_thread_id:
  - 717532865
tags:
  - Mac
  - nfs
---
Macbook에서 nfs를 설정하는 방법을 [여기][1]를 참고해서 작업했다.

/etc/exports:

    /Volumes/Workspace/mydroid/out/target/product/xxx/root -mapall=root 192.168.0.56

이렇게 하면 된다.

FreeBSD 클라이언트에서는

    $ nfsiod -n 4
    $ mount 192.168.0.14:/Users/whitekid/Documents/nfs /mnt

우선 이렇게 하면 훌륭하게 된다. 그런데 문제가 하나 있다.

nfs 클라이언트에서 파일을 만들면 서버 상에서는 모드 root가 만들 파일로 작업이 되어 버려 문제가 발생한다. 확인해보니 클라이언트 사용자의 uid가 서버에서는 없어서 root 권한으로 만드는 것 같다.

그래서 위의 exports에서 -mapall=root를 -mapall=whitekid로 변경했다. 그러니 잘 된다. ㅋㅋ

참 그런데... 시작할때 nfs 서버 띄우려면 어떻게 하는거지???

 [1]: http://www.exman.pe.kr/2009/02/mac-os-x-leopard-nfs.html