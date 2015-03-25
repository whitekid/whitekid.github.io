---
id: 1240
title: '리눅스 배포판 및 버전 확인하기: lsb_release'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1240
permalink: /archives/1240
dsq_thread_id:
  - 1219248349
tags:
  - bash
---
스크립트 작업할 때 리눅스 배포판을 확인해서 그에 따라서 작업할 경우가 있습니다. CentOS에는 어쩌구.. Ubuntu에서는 어쩌고...

배포판을 확인할 수 있는 명령은 lsb_release인데 man page에 따르면 아래와 같습니다.

> FSG lsb_release v2.0 prints certain LSB (Linux Standard Base) and Distribution information.

여기서 주요하게 필요한 것이 -id(CentOS or Ubuntu), -release(5.8, 12.04)이 두가지 인데, CentOS와 Ubuntu에 약간 차이가 있습니다.

CentOS 5.8에서는

    $ lsb_release -i -r -s
    CentOS 5.8

Ubuntu 12.04에서는

    $ lsb_release -i -r -s
    Ubuntu
    12.04

이를 스크립트에서 활용할 수 있게 tr 명령과 섞으면 아래와 같이 됩니다.

    $ /usr/bin/lsb_release -i -r -s | tr "\\n" " " | sed "s/ *\$//g "
    Ubuntu 12.04


이제 이를 이용해서 OS에 따른 대응 코드를 만들면..

    #!/bin/bash
    function get_dist(){
      /usr/bin/lsb_release -i -r -s | tr "\\n" " " | sed "s/ *\$//g "
    }

    case $(get_dist()) in
      CentOS*)
        SERVICE=/sbin/service
        SERVICE_NAME=network
        ;;
      Ubuntu*)
        SERVICE=/user/sbin/service
        SERVICE_NAME=networking
        ;;
      *)
        fatal "Not supported platform"
    esac

    $SERVICE $SERVICE_NAME restart

끄읏~