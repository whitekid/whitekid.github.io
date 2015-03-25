---
id: 982
title: 'iso 파일 마운트: losetup, fuseiso'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=982
permalink: /archives/982
dsq_thread_id:
  - 860894346
tags:
  - fuseiso
---
리눅스에서 iso 파일을 마운드하는 방법은 2가지 있습니다.

첫번째는 loop device를 사용하는 방법으로

    $ losetup /dev/loop0 /var/cache/ubuntu-12.04-amd64.iso
    $ mount /dev/loop0 /mnt
    bla bla ....
    $ umount /mnt
    $ losetup -d /dev/loop0

여기서 loop device는 중복이 되면 안되니 losetup -a로 비어있는 loop device를 확인 해야합니다.

두번째는 fuseiso를 이용하는 방법입니다.

    $ apt-get install fuseiso
    $ mount -t fuse.fuseiso -o allow_other /var/cache/ubuntu-12.04-amd64.iso /mnt
    or
    $ fuseiso /var/cache/ubuntu-12.04-amd64.iso /mnt -o allow_other

    bla bla ....
    $ umount /mnt
    or
    $ fusermount -u /mnt

  * allow_other 옵션이 없으면 다른 사용자는 접근할 수 없습니다.
  * fuseiso 명령으로 마운트하면 fusemount -u로 해제해야합니다.

fuseiso를 사용하는 것이 훨씬 간단해 보입니다.