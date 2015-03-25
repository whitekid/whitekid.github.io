---
id: 1383
title: qemu image 마운트
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1383
permalink: /archives/1383
dsq_thread_id:
  - 1583795611
categories:
  - Uncategorized
tags:
  - qemu
---
역시 기록하지 않으면 잊어먹는다... ㅡㅡ

    # modprobe nbd max_part=8  
    # qemu-nbd -c /dev/nbd0 <이미지 파일 절대경로>  
    # mount /dev/nbd0p1 /mnt  
    do something  
    # umount /mnt  
    # qemu-nbd -d /dev/nbd0  

  * <http://www.randomhacks.co.uk/how-to-mount-a-qcow2-disk-image-on-ubuntu/>