---
id: 1077
title: 'OpenStack: Quantum과 사용할 때 metadata request back routing 문제'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1077
permalink: /archives/1077
dsq_thread_id:
  - 915959953
tags:
  - MadeByKidd
  - OpenStack
---
Quantum으로 tenant network를 디자인 할 때, 딱 드는 생각은 tenant subnet을 private network을 구성하자 입니다. 물론 그렇겠죠. aws처럼 모든 생성되는 가상머신이 모두 public ip를 가질 필요는 없으니깐요. 필요한 인스턴스에 floating ip를 연결해서 외부에서 접속이 가능하게 하면 됩니다.

Quantum 가이드에 나온 [Per-tenant Routers with Private Networks][1]처럼 말이지요. 아래 그림처럼 된다면 참으로 멋진 구성이 될 텐데 말입니다. 사용자가 알아서 DMZ도 구성할 수 있고, 개발환경, production 환경의 네트웍을 똑같이 구성하여 테스트할 수 있고... 그런데 이렇게 하면서 quantum에서 예상치 못하는 결과가 발생합니다.

{% img http://docs.openstack.org/trunk/openstack-network/admin/content/figures/UseCase-MultiRouter.png" %}

그중에 하나가(overlapping_ip 문제는 별도의 문제!) instance가 처음 부팅할 때 보내는 meta data request의 패킷이 metadata api 서버로는 잘 가지만, 다시 돌아올 경로가 없습니다.

Network Node -> Controller 노드까지 경로를 보면, metadata api가 public_ip이기 때문에 instance에서는 잘 접속이 됩니다. 하지만 패킷이 다시 돌아갈 instance의 주소는 private 네트워크이고 api-host에서 다시 돌아간 패킷은 external-router에서 내부 routing 경로가 아니라고 세상 밖으로 던져져 버립니다.

그래서 metadata connection이 맺여지지 않습니다.

{% img http://docs.openstack.org/trunk/openstack-network/admin/content/figures/Quantum-PhysNet-Diagram.png" %}

metadata를 가져오지 못하면, 다른 것 다 제쳐두고라도 instance에 ssh keypair를 설정할 수 없기 때문에 가상 머신에 ssh 할 수 없는... 즉 만들었지만 접근하지 못하는 오직 ping만 할 수 있는 상황이 발생합니다. ㅎㅎ

그래서 external-router에서 내부 network으로 routing 틀어줘야 합니다.

어쨌든 그래서 아래처럼 quantum l3_agent를 소스를 기반으로 간단하게 routing 경로를 잡는 agent를 만들었고, 그 일을 하는 소스는 아래와 같습니다. 하나도 정리안된.. 막무가내 소스입니다. ㅎㅎ

tenant의 external router로 연결된 router에 대해서 해당 tenant의 subnet으로 가는 경로를 그 tenant에 기본으로 할당된 public ip로 잡아주는 것이죠. 그러면 해당 l3 name space에서는 적당히 알아서 그 instance로 보내줍니다.

    def do_single_loop(self):
        routers = self.qclient.list_routers()['routers'];
        ports = self.qclient.list_ports()

        route_info = []
        for r in routers:
            # get tenant's subnet
            subnet = self.qclient.list_ports(device_id=r['id'],
                    device_owner='network:router_interface')['ports'][0]['fixed_ips'][0]['subnet_id']
            cidr = self.qclient.show_subnet(subnet=subnet)['subnet']['cidr']

            # get the tenant's gw
            fixed_ip = self.qclient.list_ports(device_id=r['id'],
                    device_owner='network:router_gateway')['ports'][0]['fixed_ips'][0]['ip_address']
            route_info.append((cidr, fixed_ip))

        # get current route info
        old_routes = [x.split() for x in subprocess.check_output(['ip', 'route']).splitlines()[2:] if ('via' in x and 'default' not in x) ]
        old_routes = [(x[0], x[2]) for x in old_routes]

        # add new routing info
        for cidr, fixed_ip in set(route_info) - set(old_routes):
            subprocess.check_call(['route', 'add', '-net', cidr, 'gw', fixed_ip])

        # remove routing info
        for cidr, fixed_ip in set(old_routes) - set(route_info):
                subprocess.check_call(['route', 'del', '-net', cidr, 'gw', fixed_ip])


그리고 더 해야하는 게, openstack에서 외부 명령을 실행하는 것은 rootwarp을 이용해서 사용하도록 되어있습니다. 아무래도 보안상 이유겠지요. 근데 지금은 우선 잘 되는지 테스트 하기 위해서 그거 신경 안썻습니다. 나중에 시간나면 정리 합니다~ ㅎㅎ

마지막으로 소스 위치~

https://github.com/whitekid/metadata_route_agent

 [1]: http://docs.openstack.org/trunk/openstack-network/admin/content/use_cases_tenant_router.html
 [2]: http://docs.openstack.org/trunk/openstack-network/admin/content/figures/UseCase-MultiRouter.png
 [3]: http://docs.openstack.org/trunk/openstack-network/admin/content/figures/Quantum-PhysNet-Diagram.png