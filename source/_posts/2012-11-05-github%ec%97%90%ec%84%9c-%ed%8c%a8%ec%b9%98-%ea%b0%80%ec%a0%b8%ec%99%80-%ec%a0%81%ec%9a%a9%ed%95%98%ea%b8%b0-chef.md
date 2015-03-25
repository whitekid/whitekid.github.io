---
id: 1072
title: github에서 패치 가져와 적용하기 + chef
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1072
permalink: /archives/1072
dsq_thread_id:
  - 914734642
categories:
  - Uncategorized
tags:
  - chef
  - github
---
오늘 작업하다 보니 OpenStack에 몇가지 버그가 있었습니다. 버그 있는 것을 확인하고 퇴근했는데, 마침 메일링 리스트에 관련 이야기가 나오더니 커밋 링크(<https://github.com/openstack/quantum/commit/84d60f5fd477237bd856b97b9970dd796b10647e>)를 알려줍니다.

이걸 지금 chef로 작업하고 있는 것에 적용 시키기로 했지요.

    package quantum-l3-agent

    # apply l3 agent bug fix patch  
    execute "apply fetch" do  
        action :nothing

        command "wget -O /dev/stdout -q <https://github.com/openstack/quantum/commit/84d60f5fd477237bd856b97b9970dd796b10647e.patch> | \  
        patch -p1"  
        cwd "/usr/lib/python2.7/dist-packages"

        subscribes :run, "package[quantum-l3-agent]", :immediately  
    end


  1. github에서 커밋 아이디를 가져오는 것은 커밋 링크에 .patch를 붙여준다.
  2. patch는 관련 패키지를 설치할 경우만 필요하기 때문에 action :nothing을 주고 subscribe로 해당 패키지가 설치되었을 때 패치를 진행한다.
  3. 소스로 작업했으면 git apply로 할텐데.. 패키지로 작업하기 때문에 patch -p1으로..