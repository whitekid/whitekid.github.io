---
id: 1232
title: bash getopts
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1232
permalink: /archives/1232
dsq_thread_id:
  - 1148226417
tags:
  - bash
---
bash에서는 쉘 스크립트 옵션 처리를 위해서 getopts가 제공됩니다. (주의: getopt 명령과 다름)

    #!/bin/bash
    while getopts "ha:" opt; do
      case $opt in
      h)
        echo "h option"
        ;;
      a)
        echo "a option=$OPTARG"
        ;;
      ?)
        exit
        ;;
      esac
    done

    shift $((OPTIND-1))
    echo "나머지 인자: $@"

실행하는 것을 보면

    $ bash opttest.sh -h -a args hello
    h option
    a option=args
    나머지 인자: hello

간단하지요.

getopts는 bash의 builtins으로 간단한 옵션을 처리하는 것이므로 몇가지 제공하지 않는 기능이 있습니다.

  * 긴 옵션을 지원하지 않음: $ opttest.sh -long_options 안됨
  * 옵션이 뒤에 나올 수 없음: $ opttest.sh filename -h 지원하지 않음...

이런 기능을 사용하려면 getopt 명령을 사용하면 되지만... 뭔가 정말로 복잡~ 쉘 스크립트니깐 간단하게 getopts를 사용하는 것을 권장합니다.

참고: http://wiki.bash-hackers.org/howto/getopts_tutorial