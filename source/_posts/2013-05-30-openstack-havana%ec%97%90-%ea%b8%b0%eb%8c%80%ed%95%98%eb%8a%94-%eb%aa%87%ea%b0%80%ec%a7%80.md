---
id: 1284
title: 'OpenStack havana에 기대하는 몇가지...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1284
permalink: /archives/1284
dsq_thread_id:
  - 1337083440
tags:
  - OpenStack
---
요즘 grizzly 버전가지고 열심히 놀구 있습니다. 써보니깐.. private cloud까지는 훌륭히 만들 수 있을 것 같아요..

그러다 [havana 릴리스에 어떤 blue print가 있는가][1] 대충 훑어봤는데, 그중에 몇 개 이야기 해봅니다.

[**Support for Quantum L3 plugin in Devstack**][2]

l3 agent는 linux box에서 돌아가도록 되어있습니다. plugin 형태로 구축하면 hardware 장비를 조절하여 l3의 기능을 처리할 수 있겠죠. 이렇게 하면 l3 agent host에 부하가 줄어들어 보다 안정적인 서비스를 할 수 있습니다.

게다가 인프라 쪽은 s/w 보다는 h/w를 믿는 경향(?)이 있어서, 이게 더 심리적인 안정감을 줄 것 같습니다.

[**Enable loadbalancing vendors to implement their drivers - step0**][3]

이것도 위와 마찬가지로, haproxy로 되어있는 구성을 l4 장비를 이용해서 처리하는 기능이죠.

[**Dynamic DNS support for instances**][4]

aws ec2처럼 dns로 연결하고 싶습니다.

[**IPSec VPNaaS Python APIs / CRUD Operations**][5]

VPN은 public cloud를 한다면 필요한 기능입니다. havana까지는 api가 정리가 될 것 같고, 다음 릴리스에서는 뭔가 사용할 만한 결과물이 나오겠지요. OpenVPN을 이용한 client vpn, site-to-site vpn이면 훌륭합니다.

nova-network에서는 [CloudPipe][6]에서 지원했었지만, 아직 Quantum에는 없네요.

[**QoS API implementation: OpenVSwitch w/ DSCP**][7]

네트워크 서비스에 QoS는 기본이겠죠? 옆에 누군가가 엄청나게 네트워크를 써 버린다면.. ㅎㅎ

[**Ceilometer**][8]

metering입니다. 즉 어느 사용자가 얼마나 사용했는지를 나타냅니다. 효율을 측정하기 위해서는 반드시 필요하겠죠?

grizzly에 들어가긴 했지만, havana에 제대로 들어갈 예정입니다.

[**Heat**][9]

Heat는 CloudFormation 처럼 web site stack을 정의하고, 그에 따라서 API를 이용하여 OpenStack을 설정하는 콤포넌트 입니다. 이 기능보다 제가 주목하고 있는 것은 여기에 AutoScaling 기능이 들어간다는 점이죠.

역시 주 관심사가 Quantum이라 네트워크가 대부분이군요. ^^

 [1]: http://status.openstack.org/release/
 [2]: https://blueprints.launchpad.net/devstack/+spec/quantum-l3-plugin-support
 [3]: https://blueprints.launchpad.net/quantum/+spec/multi-vendor-support-for-lbaas-step0
 [4]: https://blueprints.launchpad.net/nova/+spec/dynamic-dns
 [5]: https://blueprints.launchpad.net/quantum/+spec/vpnaas-python-apis
 [6]: http://docs.openstack.org/trunk/openstack-compute/admin/content/cloudpipe-per-project-vpns.html
 [7]: https://blueprints.launchpad.net/quantum/+spec/qos-ovs-qos
 [8]: https://wiki.openstack.org/wiki/Ceilometer
 [9]: https://wiki.openstack.org/wiki/Heat