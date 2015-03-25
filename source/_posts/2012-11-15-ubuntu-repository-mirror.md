---
id: 1096
title: ubuntu repository mirror
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1096
permalink: /archives/1096
dsq_thread_id:
  - 928994714
categories:
  - Uncategorized
tags:
  - OpenStack
  - Ubuntu
---
간단하게 apt-mirror를 이용해서 apt repository mirror를 구축하는 것에 대해 정리해본다. OpenStack을 설치하는데, 원격으로 패키지를 가져오는 것이 영 맘에 안들어 간단히 구축해본 것이다.

물론 현재는 squid를 이용한 proxy를 apt-get에서 이용하도록 하고 있지만, 그래도 외부로 나가는 traffic이 있으니.. 그것을 완전히 없애려고 하는 것이다.

rsync를 이용하는 방법도 있으나, 이것은 전체를 통체로 가져오는 것이라 크기가 너무 크다. 물론 필요한 것만 가져온다고 하지만, 그게 실상 쉽지 않다. ubuntu의 경우는 .pool에 실제 파일이 들어가 있는데, 여기서 구분하기가 만만치 않아서...

    $ apt-get install apt-mirror  

/etc/apt/mirror.list 파일은 적당히 편집하여 미러링 원하는 레포지트리를 선택한다.

    deb-amd64 http://apt.opscode.com quantal-0.10 main testing  
    deb-i386 http://apt.opscode.com precise-0.10 main testing  
    deb-i386 http://apt.opscode.com quantal-0.10 main testing  
    clean http://apt.opscode.com

    deb-amd64 http://ubuntu-cloud.archive.canonical.com/ubuntu/ precise-updates/folsom main  
    deb-amd64 http://ubuntu-cloud.archive.canonical.com/ubuntu/ precise-proposed/folsom main  
    deb-i386 http://ubuntu-cloud.archive.canonical.com/ubuntu/ precise-updates/folsom main  
    deb-i386 http://ubuntu-cloud.archive.canonical.com/ubuntu/ precise-proposed/folsom main  
    clean http://ubuntu-cloud.archive.canonical.com/ubuntu  

  * apt-mirror는 기본으로 구동되는 OS의 arch에 맞춰서 해당 패키지만 가져오도록 되어있다. 따라서 deb-[amd64|i386]처럼 직접 아키텍쳐를 지정한다.

OpenStack을 chef로 자동하하기 때문에 chef repository를... OpenStack은 cloud-archive를 가져오기로 했다.

    choe@choe-pc:~/public_html$ du -sh /var/spool/apt-mirror/mirror/*  
    22M /var/spool/apt-mirror/mirror/apt.opscode.com  
    357M /var/spool/apt-mirror/mirror/ubuntu-cloud.archive.canonical.com  

설정이 되었으면 미러링이 잘 되는지 테스트 해본다.

    $ sudo apt-mirror  

별 문제 없다면 주기적으로 미러링을 하기 위해서 crontab 파일을 수정한다. apt-mirror 패키지에서 기본적인 crontab 파일을 만들어 놨지만, 주석처리되어서 실제로 미러링되지 않는다.

    $ sudo vi /etc/cron.d/apt-mirror  

참고로 ubuntu 공식 패키지 미러링은 하루에 4번 하는 것을 권장하고 있다. 따라서 crontab도 6시간 간격으로 지정해 놓으면 되겠다.  
이제 미러링 된 파일을 웹 서버에 링크하면 된다.

    $ ln -s /var/spool/apt-mirror/mirror ~/public_html/apt-mirror  

ps. precise의 전체 패키지(precise, precise-updates, precise-security, precise-proposed, precise-backports/ main, multiverse, universe, restricted)는 약 80G 정도 소요된다.