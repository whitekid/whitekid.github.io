---
id: 1047
title: 'Quantum의 Limitation...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1047
permalink: /archives/1047
dsq_thread_id:
  - 901175188
categories:
  - Uncategorized
tags:
  - OpenStack
  - Quantum
---
-fomsom 버전의 quantum에는 몇 가지 제한 사항이 있습니다. 그중에 몇가지 중요하다고 생각 되는 내용만 추려봅니다.

### overlapping ip문제

quantum에 들어가면서 새로 생긴 기능입니다. 이 기능이 뭐냐.. 간략하게 CloudStack에서 router vm을 사용하면서 그들만의 private network을 가집니다. 결국은 다른 사용자와 나는 같은 subnet을 가지는 것입니다.

KT uCloud biz에서 가상머신을 만들면 가상 머신은 사설 아이피를 가지게 되고 외부에는 NATing을 통해서 대표 아이피를 가지게 됩니다. quantum을 사용하면 이러한 가상 네트웍을 구성할 수 있는 것이죠.

그런데, 이것을 구현하면서 약간은 문제가 생겼습니다.

nova의 기존 네트워킹 모델은 가상 머신의 ip address가 unique하다는 가정 하에서 만들어 졌습니다. 그런데 이제 그런 가정 자체가 망가졌네요. ㅎㅎ 그래서 overlapping ip 기능을 사용하면 다음의 기능을 사용할 수 없습니다.

  * nova security group
  * metadata service

**security group**: security group은 computing node에서 iptable로 동작합니다. 그런데 여기서 ip로 ip table을 걸어놓으면? 그렇습니다. 다른 tenant가 같은 ip를 가질 수 있으므로 다른 사용자에게 영향을 미처 버립니다.

**metadata service**: metadata는 가상 머신 템플릿에 미리 설치된 cloud-init 패키지를 통해서 ec2 api spec을 사용하고 있습니다. ec2.. 네트워킹 모델은 모든 가상 머신의 ip가 unique합니다. 그래서 cloud-init에서 http로 보낼때 특별한 정보를 보내지 않고, 받는 측에서 request_ip 또는 X-Forward-For http header를 통하여 어떤 가상머신에서 요청이 왔는지 확인하고 응답을 합니다. 느낌이 오죠? IP는 알아냈지만 그게 어떤 tenant의 가상 머신인지 알 수 없습니다. 그리고 설사 안다고 하여도 다시 그 가상 머신으로 response를 보낼 routing 경로를 결정할 수 없습니다.

#### 해결 방법에 대한 고민?

**security group**: 해결할 수 있는 방법은 quantum-network이 사용하고 있는 namespace 기능을 compute 노드까지 사용하고, 그 namespace에 security group에 해당하는 iptables rule을 적용하면 됩니다. quantum-plugin-ovs-agent가 해야겠습니다.

**metadata service**: 이건 해결 방법이 조금 복잡할 것 같습니다. tenant의 network 밖으로 metadata request packet이 NATing할때 DNAT으로 처리합니다. destination만 metadata\_ip로 변경하는 것이죠. 여기서부터 문제인데 DNAT으로 밖으로 보내지 않고 router namespace 안에서 metadata proxy를 거치고 proxy는 request\_ip + tenant\_id를 조합하여 metadata api server로 날립니다. 그리고 metadat server는 지금처럼 request ip를 기준으로 하지말고 새로 받은 request\_ip + tenant_id로 해서 다시 받아서 던지면 되겠습니다.

### multi-host 기능 없음

quantum을 쓴다면 제 생각엔 굳이 필요 없을 기능입니다. 근데 혹시나 network host에 부하가 걱정이 되신다면 [Quantum HA][1]를 한 번 봐보세요. 아직은 저도 보지 못해서 뭐라하지는 못하지만, 문서상에는 Active-Active HA도 된다고 합니다.

### linux namespace 기능이 필요함

quantum-l3-agent, quantum-dhcp-agent가 tenant 간의 네트웍 영역을 분리하기 때문에 필요합니다. 안쓰고 사용할 수 있지만, 그럴 이유가...

Ubuntu는 지원을 하지만, CentOS는 지원하지 않습니다. CentOS도 커널은 지원(커널 2.6.4 부터 들어감)하지만, 이 기능을 사용하는 iproute 패키지가 해당 기능을 지원하지 않는 플래그를 사용하고 빌드되었습니다. 그래서 사용할 수 없죠. epel repository로 사용해보려 시도했지만 다른 패키지랑 의존성이 너무 많이 걸려서 포기했습니다.

현재로써는 Ubuntu가 OpenStack의 유일한 답입니다.

### Horizon에 통합되지 않음

quantum이 적용되었다고, 설치 후에 horizon을 보고 뭔가 하려고 하지만, 안될겁니다. [이전 포스트][2]에도 보이지만 quantum으로 만드는 과정도 복잡할 뿐더러 [만들 수 있는 네트워크 모델도 다양][3]합니다.

보시면 가장 간단한 amazon 모델부터 아주 목잡한 overlapping_ip를 사용한 모델까지... 이를 horizon에서 지원하려면.. 한참 걸릴거라 생각합니다.

### 기타

  * IPv6 지원하지 않음
  * L3 Agent의 scale limit
  * ZeroMQ is experimental
  * MetaPlugin is experimental

참고: <http://docs.openstack.org/trunk/openstack-network/admin/content/ch_limitations.html>

ps. folsom에 야심차게 quantum이 나왔지만, 지금까지 쭈욱 둘러본 결과론 아직 quantum은 beta 상태인 것 같습니다. 그리고 openvswitch을 사용해 quantum network의 기능을 모두 볼 수는 있겠지만, 이걸 사용 수준으로 사용할 수 없을 것 같고, Nicira NVP, MidoNet 같은 OVS 기반의 SDN 플렛폼을 사용하거나, Cisco NVGRE를 사용해야할 듯 합니다. 물론 내부 개발 인프라 정도로는 큰 무리 없을 것으로 생각합니다.

 [1]: http://docs.openstack.org/trunk/openstack-network/admin/content/ha_pacemaker.html
 [2]: /archives/990
 [3]: http://docs.openstack.org/trunk/openstack-network/admin/content/use_cases.html