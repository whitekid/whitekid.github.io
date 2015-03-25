---
id: 1111
title: metadata_overlapping_networks에 대한 패치가 올라와 벼렸군
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1111
permalink: /archives/1111
dsq_thread_id:
  - 931074395
categories:
  - Uncategorized
tags:
  - OpenStack
  - Quantum
---
[일전에 지금의 Quantum 구현에 overlapping networks에서 metadata를 가져오는 문제가 있고, 이에 대한 해결책에 대해서 이야기한 적이 있었죠][1]. 시간내서 이 것을 제대로 정리해서 패치를 보낼 생각이었는데... 허걱 오늘 Dream Host에서 이 문제에 대한 패치를 보냈군요.

  * <https://review.openstack.org/#>/c/16282/ - Quantum에서의 metadata proxy & agent
  * <https://review.openstack.org/#>/c/16281/ - Nova에서 handling

기본 구현을 보면 기본적인 아이디어는 제 아이디어와 비슷합니다. 역시나 namespace에서 agent를 router\_id, network\_id를 가지고 있는 agent를 띄우고, 다시 agent가 proxy 역할을 해서 실제 metadata로 proxy 형태로 request를 보내는 겁니다.

소스를 보니 깔끔하군요. 지금 적용해도 큰 무리는 없겠습니다. 다음주에 시도해봐야 겠습니다.

역시.. 빨리 했어야 하는건데. ㅡㅡ;

 [1]: http://blog.woosum.net/archives/1083