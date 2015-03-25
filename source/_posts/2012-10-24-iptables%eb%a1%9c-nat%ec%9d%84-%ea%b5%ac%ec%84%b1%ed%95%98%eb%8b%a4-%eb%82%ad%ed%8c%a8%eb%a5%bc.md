---
id: 1010
title: 'iptables로 NAT을 구성하다 낭패를...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1010
permalink: /archives/1010
dsq_thread_id:
  - 897582809
categories:
  - Uncategorized
tags:
  - linux
---
내 PC안에서 VMWare로 가상 환경을 구축하여 테스트하고 있기 때문에 가상머신이 인터넷을 접속하기 위해서 아래처럼 NAT을 구성했다.

    $ iptables -A POSTROUTING -t nat -j MASQUERADE  

아주 무리없이 잘 돌아가는 것 처럼 보였다.

가낭 VMWare에서 내부적으로 구성하는 vmnet# 간에 네트웍을 하는 것도 잘 되고 있었다. 그런데 뭔가 좀 설명할 수 없는 이상한 현상이 계속 발생 했다.

vmnet 간에 traffic이 soruce ip가 해당 네트웍의 gateway로 잡혀서 들어오는 것이다. 이것 때문에 다른 네트워간의 연결이 잘 안되는 것이었다. 한동안 문제를 찾아 해매다가 결국 찾은 것은 위에서 NAT하는 것이 문제가 있다는 것이다.

위에서 NATing을 설정할 때 input/ ouput interface를 지정하지 않았기 때문에 이녀석은 모든 interface간에 통신을 모두다 NATing 해버리는 것이다... 결국 source ip가 gateway ip(즉 호스트에 있는 해당 네트워크의 ip)로 말이지...

    $ iptables -A POSTROUTING -t nat -j MASQUERADE -o eth0  

그래서 다시 eth0로 나가는 놈만 하는 것으로 했다. 그랬더니 이제 이해할 수 있는 오류들이 나오기 시작한다.. ㅎㅎ

그래 대충한 것은 나중에 이해할 수 없는 현상을 유발한다니깐..