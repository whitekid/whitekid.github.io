---
id: 823
title: rsyslog가 CPU를 100%먹는 증상
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=823
permalink: /archives/823
dsq_thread_id:
  - 724998700
tags:
  - rsyslog
  - Ubuntu
---
Ubuntu 11.04 natty 서버가 있는데, 여기서 rsyslog가 CPU를 100% 먹는 증상이 발생한다. syslog에 아래 메시지가 지속적으로 쌓이면서 생기는 현상이다.

    Cannot read proc file system: 1 - Operation not permitted.

특정 커널과 rsyslog의 특정 버전의 조합이면 이러한 문제가 발생한다고.. 결론은 커널을 업그레이드 하단거, 아니면 rsyslog를 업그레이드 하던가...

그런데 커널을 업그레이드하면 리부팅해야하고... 돌아가는 서비스는 중단할 수 없고.. 결국은 rsyslog를 downgrade하기로 했다.

조사해보니 natty에 있는 버전은 모두 같은 버전이라 maverick으로 가기로 했다. 그래서 sources.list를 아예 maverick으로 이동..

    $ perl -p -i -e "s/natty/maverick/g" /etc/apt/sources.list
    $ apt-get update

그리고 어떤 버전이 있는지 확인해보고..

    $ apt-cache show rsyslog | grep Version
    Version: 4.6.4-2ubuntu4
    Version: 4.6.4-2ubuntu4.1
    Version: 4.2.0-2ubuntu8

그 버전으로 다운그레이드한다.

    $ apt-get install rsyslog=4.2.0-2ubuntu8

그리고 나중에 혹시나 자동으로 업그레이드 될 지 모르니 업그레이드 금지

    $ echo 'rsyslog hold' | dpkg -set-selections

스크립트로 한방

    #!/bin/bash
    release=`lsb_release -c | awk '{print $2}'`
    if [ "$release" != 'natty' ]; then
            echo 'rsyslog download only apply to natty!'
            exit
    fi

    if [ `grep -c 'natty' /etc/apt/sources.list` == 0 ]; then
            echo 'something wrong in sources.list'
            exit
    fi

    perl -p -i -e 's/natty/maverick/g' /etc/apt/sources.list
    apt-get update
    apt-get install -y --force-yes rsyslog=4.2.0-2ubuntu8
    perl -p -i -e 's/maverick/natty/g' /etc/apt/sources.list
    echo 'rsyslog hold' | dpkg --set-selections