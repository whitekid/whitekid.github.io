---
id: 1056
title: VMWare에서 hostonly vmnet간의 routing
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1056
permalink: /archives/1056
dsq_thread_id:
  - 904961517
categories:
  - Uncategorized
tags:
  - vmware
---
VMWare를 사용하면서 뭔가 복잡한 네트워크 모델을 사용하려면 hostonly vmnet을 여러개 사용하게 됩니다. VMWare를 OpenStack 테스트 용으로 사용하고 있는 저는 현재 테스트 환경에서 hostonly vmnet을 4개 사용하고 있으며 실제 환경을 그대로 묘사하기 위해서 조만간 6~7개로 늘려서 테스트할 예정입니다.

그런데 처음에 잘 모르고 지나갔다가 낭패를 본게 hostonly로 네트웍을 구성하면 host-pc에서 vmnet간에 routing을 지원하지 않습니다. 정리하면 아래와 같습니다.

  * host-pc <-> vmnet: OK
  * vmnet <-> Internet: N/A
  * vnmet <->  vmnet : N/A

당연히 되는지 알았는데, [vmnet간에 NATing이 되고 있었던 것][1]과 이로 인한 [routering filter의 간섭][2]이었죠. 이 상황을 알아채려면 상대방의 ip까지 확인 해야하는데, 그럴 일이 거의 없다가 문제가 발생해 추척하다 발견한 것이죠.. ㅎㅎ

어쨌든 vmnet간에 routing하는 방법은 [VMWare 사이트에 잘 설명][3]이 되어있습니다. 근데 여기서 방법 중에 host-pc에서 routing을 잡는 방법은 ubuntu에서 테스트하니 안됩니다. 그래서 routing을 담당하는 VM을 만들어 합니다.

간단히 정리하면

  1. hostonly network을 가지는 2개의 vm(10.20.1.10, 10.20.2.10)을 만든다. ==> 이하 internal vm
  2. 두 hostonly network을 가지는 router vm을 만든다. ==> 이하 router vm  
    eth0: NAT network for external internet access  
    eth1: 10.20.1.2  
    eth2: 10.20.2.2
  3. 여기서 .2를 가지는 것은 .1은 host-pc에 연결하고 host-pc에서 management용으로 사용할 것이므로 .2를 사용한다.
  4. router vm에서 routing이 되도록 설정한다. 간단히  
    $ systctl net.ipv4.ip_foward=1
  5. router vm에 internal vm이 외부 인터넷 접근을 위해 NATing을 설정한다. 외부 인터넷 접근 필요 하다면...  
    $ iptables -t nat -A POSTROUTING -j MASQUERADE -o eth0
  6. internal vm의 default gateway를 .2번으로 설정한다.
  7. internal vm에서 tracepath <ip>를 통해서 각각의 vm을 router vm을 통해서 접근하는지 확인한다.
  8. internal vm에서 tracepath google.com으로 외부 인터넷 연결 확인
  9. 끝..

참고..

  * iptables rule 저장은 [여기][4]를 참고

 [1]: /archives/1010
 [2]: /archives/997
 [3]: http://www.vmware.com/support/ws5/doc/ws_net_advanced_2hostonly_routing.html
 [4]: http://wiki.woosum.net/iptables#A.2BrjDQwA-