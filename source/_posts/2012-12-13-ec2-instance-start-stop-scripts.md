---
id: 1185
title: ec2 instance start/ stop scripts
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1185
permalink: /archives/1185
dsq_thread_id:
  - 972102523
categories:
  - Uncategorized
tags:
  - bash
  - ec2
---
클라우드 관련 일을 하니 역시나 사실상 표준인 ec2를 분석할 일이 생겼고, 인스턴스를 만들고 하는 일이 생겼습니다. 콘솔에서 작업하는 것이 너무 귀찮아서 간단하게 모든 region의 instance에 대해서 start/ stop/ status를 보는 스크립트를 만들어봤네요.

물론 이런 일을 해주는 뭔가가 있겠지만, 아주 간단한 일이라 찾는것도 귀찮아서...

    #!/bin/bash  
    export EC2_PRIVATE_KEY=<YOUR EC2 PRIVATE KEY FILE HERE>  
    export EC2_CERT=<YOUR EC2 CERT FILE HERE>

    this=$0  
    cmd="$1"  
    shift

    function check_expire(){  
        f=$1  
        EXPIRETIME=$2

        [ ! -f $f ] && return

        NOW=\`date +%s\` # get current time  
        FCTIME=\`stat -c %Y ${f}\` # get file last modification time  
        let "AGE=$NOW-$FCTIME"  
        if [[ $AGE -gt $EXPIRETIME ]] ; then  
            rm -f $f # this file age is more than the EXPIRETIME above, we can delete it  
        fi  
    }

    case "$cmd" in  
    stop)  
        $this | while read region instance etc; do  
        ec2-stop-instances -region $region $instance  
        done  
        ;;

    start)  
        $this | while read region instance etc; do  
        ec2-start-instances -region $region $instance  
        done  
        ;;

    regions)  
        let expire_time=60\*60\*24*7 # expire in a week  
        region_cache='/var/tmp/ec2-region-cache'  
        check_expire $region_cache $expire_time

        if [ ! -f $region_cache ]; then  
            ec2-describe-regions | awk '{print $2}' > $region_cache  
        fi

        cat $region_cache  
        ;;

        *)  
            for region in \`$this regions\`; do  
            ec2-describe-instances -region=$region | grep INSTANCE | awk "{print \"$region \" \$2 \" \" \$4 \" \" \$6}"  
            done  
        ;;  
    esac  

흠.. ec2 api .. 엄청 느리군요.. ㅡㅡ