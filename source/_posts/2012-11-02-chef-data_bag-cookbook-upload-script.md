---
id: 1065
title: chef data_bag/ cookbook upload script
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1065
permalink: /archives/1065
dsq_thread_id:
  - 910977211
tags:
  - chef
---
chef로 작업하다보면 참 불편한 것이 있다.

    $ knife cookbook upload -a -o cookbook # ---- [1]
    $ knife data bag from file hosts default.json # ---- [2]

\#1은 cookbook을 업로드하고 #2는 data bag을 업로드한다. 그런데 여기서 문제는 이 명령을 실행하는 디렉토리가 중요하다는 것이다. cookbook을 업로드 할 때는 -o로 cookbook 디렉토리를 지정해줘야하고, data bag을 업로드할 때는 chef-repository의 root에서 실행해야하는 것이다.

작업하다보면 여기저기 디렉토리를 이동하게 되는데, 그에 따라서 명령이 다르단 말이지.. 이거 귀찮아서 간단히 스크립트 만들었다. 원리는 간단하다. chef-repository의 root 디레토리에는 .chef 디렉토리에서 해당 레포지트리의 chef-server를 관리하는데, 이 디렉토리를 찾아서 해당 명령을 수행하는 것이다. 단순히 아래처럼 하면 된다.

    $ kup.sh
    $ bag.sh

이러면 자동으로 chef-repository root 디렉토리에서 cookbook, data bag upload를 수행한다.

kup.sh

    #!/bin/bash
    # upload all cookbooks
    set -e

    base_dir=`dirname $0`
    if [ -f $base_dir/cheflib.sh ]; then
        source $base_dir/cheflib.sh
    fi

    chef_dir=$(find_chef_repo)

    knife cookbook upload -a -o $chef_dir/cookbooks

bag.sh

    #!/bin/bash
    # upload all data bags

    set -e

    base_dir=`dirname $0`
    if [ -f $base_dir/cheflib.sh ]; then
        source $base_dir/cheflib.sh
    fi

    chef_dir=$(find_chef_repo)

    cd $chef_dir

    for x in `(cd data_bags; find . -name '*.json' -type f)`; do
        bag=$(echo $x | cut -d / -f 2)
        item=$(echo $x | cut -d / -f 3)

        knife data bag from file $bag $item
    done


cheflib.sh

    #!/bin/bash

    function fatal() {
        echo $@
        exit
    }

    function find_chef_repo() {
        pushd `pwd` > /dev/null

        # find .chef directory
        until [ -d .chef ]; do
            [ $(pwd) = '/' ] && break
            cd ..
        done

        [ `pwd` = '/' ] && fatal "Can't find chef repository"

        echo `pwd`
        popd > /dev/null
    }