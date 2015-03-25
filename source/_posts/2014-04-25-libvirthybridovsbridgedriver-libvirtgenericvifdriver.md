---
id: 1494
title: 'LibvirtHybridOVSBridgeDriver -> LibvirtGenericVIFDriver'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1494
permalink: /archives/1494
dsq_thread_id:
  - 2637973380
tags:
  - neutron
  - OpenStack
  - vif_driver
---
OpenStack IceHouse Release에서는 LibvirtHybridOVSBridgeDriver가 deprecated되고 LibvirtGenericVIFDriver가 사용됩니다. 그래서 LibvirtGenericVIFDriver 설정하고 인스턴스를 생성하면 havana이전에 LibvirtHybridOVSBridgeDriver에서 생성한 openvswitch - linux bridge를 사용하여 포트를 구성하죠..

그런데 havana에서 부터 있던 인스턴스는 이렇게 생성되지 않고, LibvirtOpenVswitchDriver로 생성한 것 처럼 ovs - kvm으로 직접 연결됩니다. 이렇게 되니 아래의 문제가 생기게 되죠..

  * 당연히 security group이 작동하지 않음
  * havana에서 upgrade한 인스턴스가 stop / resume / restart 등을 한 이후에는 네트워크가 되지 않음..

이게 뭘까 좀 추적해 봤습니다.

LibvirtGenericVIFDriver에서는 vif.is\_hybrid\_plug_enabled()에서 해당 포트가 hybrid인지 아닌지 판단하고 있습니다. 즉 여기까지 보면 이전에는 driver에 따라서 vif 구성이 결정되었지만, 이제는 vif port의 설정에 따라서 구성이 되는 것으로 보입니다.

그러면 이 설정은 어디 있을까.. 추적해보면

nova.network.mode.VIF 에서 아래처럼 하고 있습니다.

    def is_hybrid_plug_enabled(self):
        return self['details'].get(VIF_DETAIL_OVS_HYBRID_PLUG, False)

vif의 detail이라는 property로 들어 있는 것이죠.. 그럼 이거는 어디 있을까.. 찾아보면..

    mysql> select * from ml2_port_bindings where port_id = 'e14dbfd5-670e-405f-aaec-3796d1df1c34' \G
    *************************** 1. row ***************************
        port_id: e14dbfd5-670e-405f-aaec-3796d1df1c34
           host: compute000
       vif_type: ovs
         driver: openvswitch
        segment: d3685732-365f-4a5b-baed-8adb7b2decf3
      vnic_type: normal
    vif_details: {"port_filter": true, "ovs_hybrid_plug": true}
        profile:
    1 row in set (0.00 sec)

이렇게 DB의 설정값으로 고히 모셔져 있군요.. 이 값은

     class OpenvswitchMechanismDriver(mech_agent.SimpleAgentMechanismDriverBase):
       def __init__(self):
         super(OpenvswitchMechanismDriver, self).__init__(
           constants.AGENT_TYPE_OVS,
           portbindings.VIF_TYPE_OVS,
           {portbindings.CAP_PORT_FILTER: True,
            portbindings.OVS_HYBRID_PLUG: True})

이렇게 기본값이 True로 설정되어 DB에 들어가고 있습니다. 하지만 아래를 보면 port에 따라서 vif_details가 들어가있지 않는 포트가 있고

    mysql> select port_id, vif_details from ml2_port_bindings;
    +--------------------------------------+------------------------------------------------+
    | port_id                              | vif_details                                    |
    +--------------------------------------+------------------------------------------------+
    | 0cc863d2-adf4-49b7-9c28-1d0cf0ffd7d4 |                                                |
    | 2f2782dd-cb6b-4a37-a384-8c4883e7243d |                                                |
    | 30cb2d41-62df-44db-9faf-d6638438e71c |                                                |
    | 901b564b-5250-4215-bcad-2630b7701a85 |                                                |
    | c157a2bb-0a42-4e2a-8354-ec0560d9e28b |                                                |
    | c5ab2b09-b8cb-4381-83d4-a14a7207fdeb | {"port_filter": true, "ovs_hybrid_plug": true} |
    | c8e328fd-0279-46ff-ba45-177b95b97103 |                                                |
    | e14dbfd5-670e-405f-aaec-3796d1df1c34 | {"port_filter": true, "ovs_hybrid_plug": true} |
    +--------------------------------------+------------------------------------------------+

이 포트는 기존 havana에서 생성된 인스턴스에 연결된 포트죠..

자 그럼 이 값을 똑같이 바꾸면 어떻게 될까요..

    mysql> update ml2_port_bindings
            set vif_details = '{"port_filter": true, "ovs_hybrid_plug": true}';

녜.. 예상하는데로.. vif가 hybrid 형태로 잘 연결됩니다. ^^;

이전에는 nova reboot -hard <uuid\> 하면 인스턴스의 네트워크가 안되던 것이.. 이제는 잘 됩니다. ^^;