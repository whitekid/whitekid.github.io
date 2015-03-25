---
id: 1345
title: chef-client using lots of memory
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1345
permalink: /archives/1345
dsq_thread_id:
  - 1491708388
tags:
  - chef
---
테스트하는 서버들이 뭔가 이상하게 느려졌다. 뭔가하면서 찬찬히 둘러보는데.. 허걱 service로 돌아가는 chef-client process가 메모릴 몇G 단위로 잡아먹고 있는 것이다. 아주 정상적이지 않은 상황이다. 테스트 서버는 KVM에서 가상머신으로 돌아가고 있고, 게다가 메모리도 1G만 할당한 상황이라, 한 프로세스가 1G를 잡아먹는, 게다가 configuration management가 말이지.. 헐..

그래서 찾아보니 역시나 [알려진 문제][1]였고, service로 돌리는 경우에는 -f 옵션으로 fork해서 실행하면 된다.

chef로 chef-client를 service로 돌린 경우에는 아래처럼 environment에 넣으면된다.

    override_attributes(
      :chef_client => {
      :server_url => "https://chef.stack",
      :daemon_options => ['-f'],
      },
      .....
    )

update) 약간 된 버전의 경우 [fork bomb 버그][2]가 있습니다. 11.4.4 버전 이상으로 업그레이드 하세요.

 [1]: http://tickets.opscode.com/browse/CHEF-3432
 [2]: http://tickets.opscode.com/browse/CHEF-3367