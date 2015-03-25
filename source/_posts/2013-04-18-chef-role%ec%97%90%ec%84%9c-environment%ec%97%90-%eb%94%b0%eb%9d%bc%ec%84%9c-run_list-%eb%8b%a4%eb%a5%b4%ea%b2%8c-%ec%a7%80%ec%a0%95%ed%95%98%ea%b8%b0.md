---
id: 1243
title: 'chef: role에서 environment에 따라서 run_list 다르게 지정하기'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1243
permalink: /archives/1243
dsq_thread_id:
  - 1219274738
tags:
  - chef
---
chef를 사용하다보면 environment에 따라서 다른 run\_list가 필요한 경우가 있습니다.  이를테면 개발 환경의 서버는 개발자를 위한 편의 기능들을 미리 집어넣는 경우지요. 아마도 개발머신에서는 개발자의 public\_key를 모두 등록해서 개발 머신에는 각자의 키로 접속할 수 있고, production에서는 그 기능을 빼는 경우죠.

role에서는 이런 경우를 위해서 env\_run\_lists를 이용하여 기능을 재공하고 있습니다.

roles/base.rb

    name "base"
    description "base role for all node"

    default_roles = [
      "recipe[chef-client::service]", "recipe[chef-client::config]", "recipe[ntp]",
    ]

    run_list default_roles
    env_run_lists "_default" => default_roles,
                  "devel" => default_roles + ["recipe[my_cookbook::developers_public_keys]"]


굳이 설명 안해도 코드만 보고 다 알겠죠? ㅎㅎ