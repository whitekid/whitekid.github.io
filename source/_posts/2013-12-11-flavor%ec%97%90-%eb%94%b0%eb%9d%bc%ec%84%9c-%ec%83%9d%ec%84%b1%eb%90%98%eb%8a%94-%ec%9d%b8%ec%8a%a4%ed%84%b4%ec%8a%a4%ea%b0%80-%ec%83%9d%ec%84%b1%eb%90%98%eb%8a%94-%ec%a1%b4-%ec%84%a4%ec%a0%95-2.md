---
id: 1444
title: flavor에 따라서 생성되는 인스턴스가 생성되는 존 설정하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1444
permalink: /archives/1444
dsq_thread_id:
  - 2043169302
categories:
  - Uncategorized
tags:
  - availability-zone
  - flavor
  - host-aggregate
  - nova
  - OpenStack
---
가끔 이런 경우가 있습니다.

> 이 flavor로 만드는 인스턴스는 성능이 좋아야 합니다. 그러니 이 flavor로 만드는 인스턴스는 성능이 좋은 물리 호스트에 만들어 주세요.

SSD를 사용하는 호스트, RAM이 특별하게 많이 필요한 인스턴스, Disk I/O가 많이 필요한 인스턴스 등등 이런 요청은 다양합니다. 그런 경우는 이전에서 만든 [host aggregate][1]와 flavor를 연결하여 특정 flavor는 특정 존에 생성되도록 강제 할 수 있습니다.

인스턴스를 어떤 compute node에 위치시킬 것인가는 nova-scheduler에 의해서 결정되므로, nova-scheduler에서 flavor와 host aggregate를 동시에 고려하는 Filter가 있으면 되겠죠. 녜.. 당연히 있고, 그 것이 [AggregateInstanceExtraSpecsFilter][2] 입니다.

당연히 이 필터를 nova scheduler 필터에 추가하고..

/etc/nova.conf:

    scheduler_default_filters=AggregateInstanceExtraSpecsFilter,AvailabilityZoneFilter,RamFilter,ComputeFilter  

특정 zone에 인스턴스를 생성할 flavor에 아래처럼 flavor의 extra-specs에 넣습니다.

    # nova flavor-show 1  
    +----------------------------+----------------------------+  
    | Property | Value |  
    +----------------------------+----------------------------+  
    | name | m1.tiny |  
    | ram | 512 |  
    | OS-FLV-DISABLED:disabled | False |  
    | vcpus | 1 |  
    | extra_specs | {u'locate_zone1&': u'true'} |  
    | swap | |  
    | os-flavor-access:is_public | True |  
    | rxtx_factor | 1.0 |  
    | OS-FLV-EXT-DATA:ephemeral | 0 |  
    | disk | 0 |  
    | id | 1 |  
    +----------------------------+----------------------------+  

이제 host aggregate에서 해당 extra-specs에 locate_zone1=true가 설정된 인스턴스를 만들 수 있게 meta-data로 설정하면 됩니다.

    # nova aggregate-set-metadata 1 locate_zone1=true  
    Aggregate 1 has been successfully updated.  
    +----+------+-------------------+----------------------------------+--------------------------------------------------------------+  
    | Id | Name | Availability Zone | Hosts                            | Metadata                                                     |  
    +----+------+-------------------+----------------------------------+--------------------------------------------------------------+  
    |  1 | agg1 | zone1             | [u'compute000&', u'compute001&'] | {u'locate_zone1&': u'true', u'availability_zone': u'zone1&'} |  
    +----+------+-------------------+-------------------------------+-----------------------------------------------------------------+  

이제 flavor 1로 인스턴스를 만들면 zone1에 생성되는 것을 볼 수 있습니다.

    # nova list -all-tenants -fields flavor,host  
    +--------------------------------------+--------+------------+  
    | ID                                   | Flavor | Host       |  
    +--------------------------------------+--------+------------+  
    | 0a3e3ff7-fa2e-47c5-b820-25d3c6f005aa | 1      | compute001 |  
    | 2211556d-abef-48d1-91fa-3fd2bc4286d5 | 1      | compute000 |  
    | 70535b26-a284-4ad3-8f14-e09bde385b97 | 1      | compute000 |  
    | ba8bf1ce-acd8-4b25-8a08-56ff71283c32 | 1      | compute001 |  
    +--------------------------------------+--------+------------+  

참고 링크

  * <http://russellbryantnet.wordpress.com/2013/05/21/availability-zones-and-host-aggregates-in-openstack-compute-nova/>
  * <http://docs.openstack.org/grizzly/openstack-compute/admin/content/host-aggregates.html>

 [1]: http://blog.woosum.net/archives/1454 "host-aggregation을 availability-zone으로 사용하기"
 [2]: http://docs.openstack.org/grizzly/openstack-compute/admin/content/scheduler-filters.html#aggregateinstanceextraspecsfilter