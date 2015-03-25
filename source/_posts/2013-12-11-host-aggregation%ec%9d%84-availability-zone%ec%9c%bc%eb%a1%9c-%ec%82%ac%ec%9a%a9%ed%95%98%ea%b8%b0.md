---
id: 1454
title: host-aggregation을 availability-zone으로 사용하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1454
permalink: /archives/1454
dsq_thread_id:
  - 2043606883
tags:
  - availability-zone
  - host-aggregate
  - nova
  - OpenStack
---
[Host aggregation](http://docs.openstack.org/grizzly/openstack-compute/admin/content/host-aggregates.html)은 Availability zone 처럼 사용할 수 있습니다.

기본 설치 상태에서 보면 아래처럼 nova zone만 보입니다.

    # nova availability-zone-list
    +-----------------------+----------------------------------------+
    | Name                  | Status                                 |
    +-----------------------+----------------------------------------+
    | internal              | available                              |
    | |- control0           |                                        |
    | | |- nova-conductor   | enabled :-) 2013-12-11T08:36:33.000000 |
    | | |- nova-consoleauth | enabled :-) 2013-12-11T08:36:36.000000 |
    | | |- nova-cert        | enabled :-) 2013-12-11T08:36:36.000000 |
    | | |- nova-scheduler   | enabled :-) 2013-12-11T08:36:31.000000 |
    | nova                  | available                              |
    | |- compute003         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T08:36:31.000000 |
    | |- compute002         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T08:36:31.000000 |
    | |- compute001         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T08:36:31.000000 |
    | |- compute000         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T08:36:31.000000 |
    +-----------------------+----------------------------------------+

여기서 host-aggregate를 2개 만들고 각각에 2개씩 compute node를 넣어봅니다.

    # nova aggregate-create agg1
    # nova aggregate-create agg2
    # nova aggregate-list
    +----+------+-------------------+
    | Id | Name | Availability Zone |
    +----+------+-------------------+
    | 1  | agg1 | None              |
    | 2  | agg2 | None              |
    +----+------+-------------------+
    # nova aggregate-add-host 1 compute000
    Aggregate 1 has been successfully updated.
    +----+------+-------------------+-----------------+----------+
    | Id | Name | Availability Zone | Hosts           | Metadata |
    +----+------+-------------------+-----------------+----------+
    | 1  | agg1 | None              | [u'compute000'] | {}       |
    +----+------+-------------------+-----------------+----------+
    # nova aggregate-add-host 1 compute001
    Aggregate 1 has been successfully updated.
    +----+------+-------------------+--------------------------------+----------+
    | Id | Name | Availability Zone | Hosts                          | Metadata |
    +----+------+-------------------+--------------------------------+----------+
    | 1  | agg1 | None              | [u'compute000', u'compute001'] | {}       |
    +----+------+-------------------+--------------------------------+----------+
    # nova aggregate-add-host 2 compute002
    Aggregate 2 has been successfully updated.
    +----+------+-------------------+-----------------+----------+
    | Id | Name | Availability Zone | Hosts           | Metadata |
    +----+------+-------------------+-----------------+----------+
    | 2  | agg2 | None              | [u'compute002'] | {}       |
    +----+------+-------------------+-----------------+----------+
    # nova aggregate-add-host 2 compute003
    Aggregate 2 has been successfully updated.
    +----+------+-------------------+--------------------------------+----------+
    | Id | Name | Availability Zone | Hosts                          | Metadata |
    +----+------+-------------------+--------------------------------+----------+
    | 2  | agg2 | None              | [u'compute002', u'compute003'] | {}       |
    +----+------+-------------------+--------------------------------+----------+

여기서 다시 nova availability-zone-list 하면 아무런 변화기 없습니다. nova aggregate-list 결과에 보면 "Availability Zone"이라는 항목이 있습니다. 녜.. host aggregate에 zone 설정이 없어서 그런 것이죠..

다시 host aggregate에 availability zone을 설정해보죠.

    # nova aggregate-update 1 agg1 zone1
    Aggregate 1 has been successfully updated.
    +----+------+-------------------+--------------------------------+----------------------------------+
    | Id | Name | Availability Zone | Hosts                          | Metadata                         |
    +----+------+-------------------+--------------------------------+----------------------------------+
    | 1  | agg1 | zone1             | [u'compute000', u'compute001'] | {u'availability_zone': u'zone1'} |
    +----+------+-------------------+--------------------------------+----------------------------------+
    # nova aggregate-update 2 agg1 zone2
    Aggregate 2 has been successfully updated.
    +----+------+-------------------+--------------------------------+----------------------------------+
    | Id | Name | Availability Zone | Hosts                          | Metadata                         |
    +----+------+-------------------+--------------------------------+----------------------------------+
    | 2  | agg1 | zone2             | [u'compute002', u'compute003'] | {u'availability_zone': u'zone2'} |
    +----+------+-------------------+--------------------------------+----------------------------------+

이제 다시 availability zone을 보면

    # nova availability-zone-list
    +-----------------------+----------------------------------------+
    | Name                  | Status                                 |
    +-----------------------+----------------------------------------+
    | internal              | available                              |
    | |- control0           |                                        |
    | | |- nova-conductor   | enabled :-) 2013-12-11T09:01:15.000000 |
    | | |- nova-consoleauth | enabled :-) 2013-12-11T09:01:09.000000 |
    | | |- nova-cert        | enabled :-) 2013-12-11T09:01:09.000000 |
    | | |- nova-scheduler   | enabled :-) 2013-12-11T09:01:14.000000 |
    | zone1                 | available                              |
    | |- compute001         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T09:01:15.000000 |
    | |- compute000         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T09:01:15.000000 |
    | zone2                 | available                              |
    | |- compute003         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T09:01:15.000000 |
    | |- compute002         |                                        |
    | | |- nova-compute     | enabled :-) 2013-12-11T09:01:15.000000 |
    +-----------------------+----------------------------------------+

녜.. 그렇습니다. host aggregate를 availability zone으로 사용할 수 있습니다.

그리고 이렇게 만들어진 존을 지정하여 인스턴스를 만드는 것은 아래처럼 할 수 있습니다.

    # nova boot --flavor=<flavor> --nic net-id=<net-id> \
        --image=<image-id> --key_name=<keyname> \
        --availability-zone zone1:compute000 <instance-name>

admin 권한으로는 compute node까지 지정이 가능하고, admin이 아닌 경우는 zone 까지만 지정 가능합니다.

참고링크: http://russellbryantnet.wordpress.com/2013/05/21/availability-zones-and-host-aggregates-in-openstack-compute-nova/