---
layout: post
title: "neutron default security group"
date: 2015-03-25 14:23:42 +0900
comments: true
categories: 
tags:
    - openstack
    - neutron
    - security-group
---

Security Group은 기본설정으로 모든 inbound traffic을 차단하고 모든 outbound traffic을 허용합니다. 그리고 같은 tenant의 인스턴스는 모든 traffic을 허용하도록 되어 있습니다.

Public Cloud를 고려하는 상황이라면 이 정책은 맞는 정책입니다. 그런데 Private Cloud라면 조금 상황이 달라집니다. 일반적으로 회사에서는 내부의 traffic에 대한 제한을 걸지않고 있습니다(상황은 다 다르겠지만). 그리고 보안팀의 정책에 따라서 보안구역을 나누어 ACL을 설정하는 형태로 운영이 되겠죠.

더 구제적으로 따져가서 오픈스택을 개발용 인프라로 사용할 경우, 개발자에게 모든 트래픽이 막혀있으니, 필요할 경우는 알아서 여세요..가 그리 쉽지 않다. 그리고 사용하다보니 결국 개발자들은 모든 tcp/udp traffic을 허용하고 사용하고 있다.

특히나 인스턴스 만들고 ping, ssh가 안되니, 처음 접하는 사람은 조금 당황하는 현상이 발생한다.

nova-network을 사용하는 경우는 default security의 rule을 수정하는 기능이 들어가 있다.

    nova secgroup-list-default-rules
    nova secgroup-add-default-rule
    nova secgroup-delete-default-rule

하지만 neutron에는 이 기능이 작동하지 않는데, 이는 해당 API의 기능을 neutron에서 구현하지 않고 있다. 물론 이 기능을 구현하기 위한 [blueprint](https://blueprints.launchpad.net/neutron/+spec/default-rules-for-default-security-group)도 등록이 되어 있지만, 현재는 abandon 상태다.

어쨌든 우리는 neutron을 사용하고 있고, 이게 불편해서 그냥 모든 inbound traffic을 혀용한다면 간단하게 소스를 수정하면 된다.

    diff --git a/neutron/db/securitygroups_db.py b/neutron/db/securitygroups_db.py
    index 92dcc7a..2438b53 100644
    --- a/neutron/db/securitygroups_db.py
    +++ b/neutron/db/securitygroups_db.py
    @@ -143,13 +143,12 @@ class SecurityGroupDbMixin(ext_sg.SecurityGroupPluginBase):
                         tenant_id=security_group_db['tenant_id']))
                 for ethertype in ext_sg.sg_supported_ethertypes:
                     if default_sg:
    -                    # Allow intercommunication
    +                    # Allow all ingress traffic
                         ingress_rule = SecurityGroupRule(
                             id=uuidutils.generate_uuid(), tenant_id=tenant_id,
                             security_group=security_group_db,
                             direction='ingress',
    -                        ethertype=ethertype,
    -                        source_group=security_group_db)
    +                        ethertype=ethertype)
                         context.session.add(ingress_rule)

                     egress_rule = SecurityGroupRule(

이렇게 하면 이제 모든 inbound traffic은 아주 잘 들어온다.
