---
id: 1508
title: network fixed-ip scheduler? IP Address가 부족해요..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1508
permalink: /archives/1508
dsq_thread_id:
  - 2682345286
categories:
  - Uncategorized
tags:
  - neutron
  - OpenStack
  - scheduler
---
24비트 네트워크를 할당하고 사용한다고 합시다... 그렇다면 256 - 1(Router) - 1(Boradcast) - 1 (dhcp) - 10(Reserved) = 234개의 인스턴스를 생성할 수 있습니다. 물론 LBaaS를 사용한다면 좀더 아이피를 사용할 수 있겠지요.. 뭐.. 각설하고..

자... 이렇게 인스턴스가 만들어지다 보면 IP를 소진하게 되고.. IP를 다 사용하면 네트워크를 추가해야 합니다. 그렇다면 NET1, NET2 이렇게 2개의 네트워크가 생성이 되었다면  여기서 문제가 생깁니다.

## 인스턴스를 생성할 때 네트워크 선택이 필수 입니다.

하지만, 사용자 입장에서 어떤 네트워크에 IP가 남아있는지 확인해서, 여유있는 네트워크를 생성해야 합니다. 안그러면 당연히 에러가 나겠죠.. 그러면 인스턴스를 생성할 때 사용자가 체크해야하는 단계가 하나 생기는 겁니다.

네트워크를 선택하지 않으면, Havana이전의 릴리스에서는 모든 네트워크에 연결되어 인스턴스가 생성이 됩니다(Icehouse 버전은 cli에서는 동일하게 동작하고 Horizon에서는 오류가 발생합니다). 일반적으로 특별한 이유가 없으면 이렇게 사용하지 않을 것이므로, 명시적인 사용을 하지 않는 경우가 아니라면 모든 네트워크에 연결되는 인스턴스를 생성하는 것도 좋지 않습니다. 게다가 인터페이스의 순서도 자동으로 설정이 되어 의도하지 않는 결과가 발생할 수 있는 것이죠.

## 네트워크 자동 선택이 필요하다.

그래서 고민고민 하다가, 사용자 입장에서는 AWS처럼 굳이 네트워크를 선택하지 않으면 자동으로 알아서 적당한 네트워크를 선택해서 만들어주면 되겠다고 생각을 했습니다.

이른바 Network Scheduler 기능이 있으면 좋겠죠.. (Network Scheduler로 하면 L2, L3, LB 등등등 많기 때문에 좀 애매하지만..)

당연히도 이런 기능은 nova 또는 neutron에 없습니다. (당연하죠?)

## nova-api를 수정

처음에는 이러한 기능을 neutron에서 처리할 것으로 생각했지만, 소스를 분석해보니 nova-api에서 network를 지정해주지 않으면 모든 사용 가능한 네트워크에 인터페이스를 생성합니다.

nova.compute.api.API.\_create\_instance에서 requested\_network로 사용자가 요청한 네트워크가 없으면 자동으로 인스턴스를 생성 가능한 네트워크로 request\_network를 채워주면 되겠다는 생각에 작업해 봤습니다.

nova.compute.api.API.\_create\_instance: 에 아래처럼 request_networks가 없을 경우 scheduling을 처리할 코드를 넣고..

    def _create_instance(self, context, instance_type, ....):  
      .... foo bar ....  
      if not requested_networks:  
      net_id = self.network_api.schedule_network_for_instance(context, max_count)  
      if net_id: requested_networks = [(net_id, None, None)]  

nova.compute.neutronv2.api.API.schedule\_network\_for\_instance: 에서 실제로 선택하는 기능을 추가합니다.

    def schedule_network_for_instance(self, context, num_instances):  
      neutron = neutronv2.get_client(context)  
      nets = self._get_available_networks(context, context.project_id,  
      neutron=neutron)
      
      nets = sorted(nets, key=lambda x: x['name'])
      
      quotas = neutron.show_quota(tenant_id=context.project_id)['quota']  
      ports_needed = num_instances  
      for net in nets:  
        ports = neutron.list_ports(tenant_id=context.project_id)['ports']  
      free_ports = quotas.get('port') - len(ports)
      
      if free_ports >= ports_needed:  
      return net['id']  

## 사용 방법

사용 방법은 동일합니다.

자동으로 네트워크를 선택하게 하려면 아래처럼 NIC 옵션을 빼고 명령을 주면, 자동으로 네트워크를 선택해서 만들어 줍니다.

    $ nova boot -flavor m1.tiny -image <image-uuid> test  

기존에는 모든 사용 가능한 네트워크에 연결된 인스턴스를 만들었지만요..

당연히 네트워크를 지정하면 해당 네트워크에 연결된 인스턴스를 만듭니다.

    $ nova boot -flavor m1.tiny -image <image-uuid> -nic net-id=<net-uuid> test  

## 개선 방향

### scheduling 방법...

지금 scheduler는 name을 기준으로 정렬한 후 순차적으로 할당하게 되어있습니다. 아주 단순한 scheduling이죠.. 이를 여러 상황에 맞게 조졀하는 기능이 필요하겠습니다.

### 자동 선택 가능한 네트워크..

상황에 자동으로 선택 가능한 네트워크를 지정할 필요가 있습니다. 특정 네트워크는 개발용도, 특정 내트워크는 프러덕션 용도로 분리된 경우라면요..

## 추가...

이거 추적하면서 알게된 몇 가지 사항들 추가하죠..

### multi subnet

network에는 subnet이 1:1 관계가 아니라 1:n의 관계입니다.

아래를 보시면 test network에 subnet이 2개가 할당이 되어있습니다.

    $ neutron net-list -name test  
    +-------------------------------------+------+----------------------------------------------------+  
    | id | name | subnets |  
    +-------------------------------------+------+----------------------------------------------------+  
    | 2e6c7fde-63f1-4289-8bb6-93c5e0d682f7 | test | 20e0d8cc-ff0a-410b-92d1-da5902ff851b 10.10.201.0/24 |  
    | | | 37f69bb4-0cbb-45ca-8877-0a1189318316 10.10.200.0/24 |  
    +-------------------------------------+------+----------------------------------------------------+  

그렇다면 이 포스트에서 제기하는 IP 문제를 해결할 수 있다고 생각이 드실겁니다. 녜.. 물론 그렇습니다. 그런데, 이건 실제로 네트워크 상에서 구현이 될 때 약간 애매하나 것이 있습니다.

위의 상황은 하나의 네트워크(VLAN) 상에 다른 서브넷의 패킷이 같이 공존하는 상황인데, 네트워크 엔지니어에게 자문을 구해보니 네트워크 상에서는 큰 문제가 없고 실제로 이렇게 운영하는 경우도 있다고 합니다.

다만, 이렇게 하는 경우 switch에서 작업할 때 저렇게 port에 여려 subnet이 있다는 사실을 인지하고 작업을 진행해야하기 때문에, 작업상 실수가 일어날 가능성이 높아서, 가능은 하지만 권장은 하지 않는다고 합니다.

이러한 문제로 실제로 사용하기에는 좀 그렇습니다. 하지만 네트워크 엔지니어가 문제가 없다고 하면 이런 방법도 괜찮은 방법으로 생각합니다.

### multi provider network

또 다른 방법으로 multi provider network라는 기능이 있습니다.

기존으 provider network은 하나의 segmentation(VLAN)을 사용할 수 있지만, multi provider network을 한 네트워크 안에서 여러 segmentation을 허용할 수  있다고 합니다.

아래 명령으로 확인하면 해당 extension이 있는지 확인할 수 있습니다.

    $ neutron ext-list | grep provider  
    | provider | Provider Network |  
    | multi-provider | Multi Provider Network |  

네트워크 생성은 조금 복잡하게 만들어 집니다. 당연히 여러 segmentation을 넣어야되니 그렇겠죠.

    $ neutron net-create test -segments type=dict list=true \  
    provider:network_type=vlan,provider:physical_network=default,provider:segmentation_id=200 \  
    provider:network_type=vlan,provider:physical_network=default,provider:segmentation_id=201 \  
    -shared

    $ neutron net-show test  
    +----------------+---------------------------------------------------------------------------------------------------------    ---+  
    | Field | Value |  
    +----------------+---------------------------------------------------------------------------------------------------------    ---+  
    | admin\_state\_up | True |  
    | id | 2e6c7fde-63f1-4289-8bb6-93c5e0d682f7 |  
    | name | test |  
    | router:external | False |  
    | segments | {"provider:network\_type": "vlan", "provider:physical\_network": "default", "provider:segmentation_id": 200} |      
    | | {"provider:network\_type": "vlan", "provider:physical\_network": "default", "provider:segmentation_id": 201} |  
    | shared | True |  
    | status | ACTIVE |  
    | subnets | 20e0d8cc-ff0a-410b-92d1-da5902ff851b |  
    | | 37f69bb4-0cbb-45ca-8877-0a1189318316 |  
    | tenant_id | 6b3f1bffc77345468198c85d995479a2 |  
    +----------------+---------------------------------------------------------------------------------------------------------    ---+  

이것도 이 포스트의 문제를 해결할 수 있는 좋은 방법입니다. multi-subnet을 사용하는 것에 비해서 네트워크 엔지니어의 작업 오류를 줄 일 수 있겠죠..

하지만, 당연히도(^^) 이 게 잘 되지 않습니다.

이유는 ML2 Plugin에는 구현이 잘 되어있지만, OpenVSWitch Mechnism driver에는 이 지원이 빠져있습니다. (역시나...)

그래서 실제 구성 및 인스턴스 생성은 잘 되는데, 외부 VLAN과 실제 연결할때 ovs flow control에서 생성할 때 지정한 200, 201 두 가지 vlan에 대한 tag mapping을 설정해 줘야하는데, 하나만 설정하는 문제가 있더군요.. (이건 명백한 버그..)

그래서 이를 구현한 코드를 좀 보니깐... nicira nvp, cisco 에서 multi provider network을 위한 기능을 추가했고, 이 사람들은 OVS에 기능을 추가하지 않았군요.. ㅎㅎ

뭐.. 조금 더 삽질하면 될 것 같기는 하지만. .여기서는 이만.