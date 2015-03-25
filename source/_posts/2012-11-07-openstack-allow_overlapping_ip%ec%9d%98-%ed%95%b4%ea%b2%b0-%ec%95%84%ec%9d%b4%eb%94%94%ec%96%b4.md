---
id: 1083
title: 'OpenStack: allow_overlapping_ip의 해결 아이디어 간단 구현'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1083
permalink: /archives/1083
dsq_thread_id:
  - 918225406
categories:
  - Uncategorized
tags:
  - MadeByKidd
  - OpenStack
---
quantum 제한사항에서 [allow\_overlapping\_ip 기능이 안된다][1]고 이전에 포스트 했었고, 이에 대해서 잠깐 생각을 하다가 아이디어가 떠올라서 몇 가지 작업했더니 대충은 동작합니다.

안되는 근본 원인은 instance의 ip를 가지고 어떤 instance인지 특정할 수 없다는 것이었는데, 여기다 tenant-id를 같이 조합하면 된다는 것이죠. 문제는 어떻게 tenant-id를 넘기느냐 였는데, http를 사용하니 header에 집어넣고, 이 header를 집어 넣는 역할을 하는 proxy를 l3 router namespace에서 동작하면 되는 것입니다.

이제 아래처럼 metadata 요청 흐름이 바뀌는 것이죠.

  * instance -> DNAT -> metadata server
  * instance -> proxy in router namespace -> metadata server

prox 서버는 간단히 twisted를 이용해서 만들었습니다.

적용 순서는...

  1. metadata patch 적용: <https://github.com/whitekid/nova/commit/de9b371a4667dd66f510093a1e207bc7f9e02c6d>
  2. metadata api restart

router name space에서

  1. lo device에 metadata ip 추가  
    $ ip netns exec qrouter-XXXX ip addr add 169.254.169.254/32 device lo
  2. DNAT하는 iptables rule 삭제 
    <pre>$ ip netns exec qrouter-XXXX
$ iptables -t nat -L quantum-l3-agent-PREROUTING | grep 169.254.169.254 > /dev/null && iptables -t nat -D quantum-l3-agent-PREROUTING 1
$ exit</pre>

  3. metadata proxy 실행 
    <pre>$ ip netns exec qrouter-XXX python metadata-proxy [real-metadata-api-server-ip] [tenant-id]</pre>

그냥 단순히 아이디어 검증 차원이라, 실제로 쓰고자 한다면 많은 추가 작업이 필요할 것 같습니다.

<https://github.com/whitekid/metadata_proxy>

ps. 아.. 영어 어렵다.. ㅡㅡ

 [1]: http://blog.woosum.net/archives/1047