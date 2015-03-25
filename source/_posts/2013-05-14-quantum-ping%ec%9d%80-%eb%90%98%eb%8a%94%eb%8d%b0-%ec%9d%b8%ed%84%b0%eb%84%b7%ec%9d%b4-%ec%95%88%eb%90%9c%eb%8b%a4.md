---
id: 1262
title: 'Quantum: ping은 되는데 인터넷이 안된다.'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1262
permalink: /archives/1262
dsq_thread_id:
  - 1286495718
tags:
  - OpenStack
  - Quantum
---
OpenStack 네트워크를 OpenVSwitch, GRE tunneling으로 구성하였을 경우에, 인스턴스에서 외부로 ping은 나가나 인터넷이 원할하지 않는 현상을 보일 때가 있습니다. 아주 일반적이지 않는 경우죠.

ping이 아주 잘 되고, dns lookup까지 아주 잘되면 일반적으로 인터넷이 잘된다고 할 수 있으나,

    $ curl google.com # ---- [1]
    $ curl www.google.com # ---- [2]

\[1] 번의 경우에는 잘 되고, \[2]번은 안되는 경우가 발생합니다.

이 것은 gre tunneling의 특성때문에 발생하는 것으로 CISCO의  [Why Can't I Browse the Internet when Using a GRE Tunnel?][2]에 아주 자세히 설명이 나와있습니다.

간단히 요약하면 gre tunneling을 하려면 packet을 gre header를 포함하여 encapsuling하게 되는데, 이 때문에 tcp/ip 패킷에 한번에 담을 수 있는 사이즈는 1500(기본 MTU) - 24(GRE Header) = 1476이 됩니다. 그런데 gre tunneling 외부(L3 agent)에서 부터는 mtu가 1500이므로 상대방 웹 서버에서는 mtu인 1500으로 보내주는데, gre tunneling 안에서는 이를 분리하여 보낼 수 없으므로 최대 패킷 크기 mtu(SMSS)를 조정하는 ICMP를 호출합니다. 여기서 ICMP로 mtu 조절하는 명령이 막히면 서로 mtu 조절에 실패하여 통신이 막히는 것으로 보이는 것이죠..

위의 경우를 보면 [1]은 전송되는 데이터가 작아서 packet 한번에 전송됩니다. 그래서 SMSS를 조절할 필요가  없는데, [2]의 경우는 패킷이 커서 SMSS를 조절할 필요가 있는데, 여기서 막히는 것입니다.

이를 해결하는 간단한 방법은 인스턴스 인터페이스의 mtu를 적당히 조절하면 됩니다. 저의 경우는 1454로 조절하면 되더군요. 그리고 이를 모든 인스턴스에 적용하게 하려면 quantum-dhcp-agent의 옵션으로 두면 되겠습니다.

그리고 production에 간다면, 당연히 인스턴스의 mtu를 조절하는 방법은 이상하고, gre tunneling이 사용하는 switch, interface의 mtu를 1524 이상으로 조절하는 것이 좋겠습니다.

 [2]: http://www.cisco.com/en/US/tech/tk827/tk369/technologies_tech_note09186a0080093f1f.shtml "Why Can't I Browse the Internet when Using a GRE Tunnel?"