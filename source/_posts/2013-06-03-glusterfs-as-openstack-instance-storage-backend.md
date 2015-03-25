---
id: 1292
title: glusterfs as OpenStack instance storage backend
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1292
permalink: /archives/1292
dsq_thread_id:
  - 1354260097
categories:
  - Uncategorized
tags:
  - glusterfs
  - live_migration
  - OpenStack
---
OpenStack에서 Live migration을 지원하려면 기본적으로 shared storage가 있어야 한다. 지금 요구사항 중의 하나가 장애에 대비한 live migration이라 shared storage를 설정해야했다.

요즘 대세는 Ceph이라고, 저번 오픈스택 모임에서 그랬는데, 잠깐 Ceph를 시도해본 결과, 설치과정이 그리 맘에 들지 않습니다. 그래요 완전히 개인적인 생각입니다.

그래서 상대적으로 쉬운 GlusterFS를 먼저 해보고려 합니다.

GlusterFS를 한다면 [GlusterFS is Ready for OpenStack][1]이라고 발표를 했는데, 문제는 아직 Beta 단계인 3.4 이야기이고, 자신들의 OpenStack Distribution인 [RDO][2]에 테스트 해 봤다는 이야기입니다.

내용을 보면 Instance Storage도 libgfapi를 이용해서 glusterfs에 직접 IO를 통해서 합니다. 그래서 중간의 파일 시스템에서 생기는 성능/ 캐쉬 등등의 문제를  없앴죠. Ceph도 그렇구요. 이거 제대로 된다면 바로 적용해 보려고 했지만... Ubuntu Package가 아직 없어요.. 그래서 정식 버전이 나오기 전에는 3.2에서 테스트 하기로 했습니다. 아고 서론이 길었어요.

### Overview

테스트 환경에서는 별도의 glusterfs cluster를 구성하지 않고 glusterfs server는 compute node에 설치하기로 했습니다(아마도 별 문제 없으면 실제로도 그렇게 갈 생각이구요). 그리고 각 compute node는 /var/lib/nova/instance를 glusterfs localhost로 마운트 합니다.

### Gluster FS 설치

간단히 각 compute node에서 설치합니다.

    $ apt-get install glusterfs-server  

### Peer probe

    $ gluster peer probe compute02.stack  
    $ gluster peer probe compute03.stack  

### Volume create

    $ gluster volume create vm-instances replica 3 \  
    compute01.stack:/gluster/instances \  
    compute02.stack:/gluster/instances \  
    compute03.stack:/gluster/instances  

물론 각 compute node에는 만드려는 brick의 디렉토리인 /gluster/instance를 미리 만들어야합니다.

### Volume start

    $ gluster volume start vm-instances  

### 마운트

마운트 하기 전에 아무런 인스턴스들이 생성되지 않은 상태로 합니다.

    $ mount -t glusterfs localhost:vm-instances /var/lib/nova/instances  

마운트하면 인스턴스 디렉토리의 소유권이 root.root가 되는데, nova.nova로 수정합니다. 신기하게도 이거 바꾸면 다른 노드도 같이 수정이 되는군요.

    $ chown nova.nova /var/lib/nova/instances  

한쪽 compute 노드에서 파일을 생성하면, 다른 컴퓨터 노드에도 같이 보이는 것을 확인할 수 있습니다.

### nova.conf 설정

live migration을 하지 않는다면, 별도로 수정할 내용은 없습니다. 파일 권한이 제대로 되어있다면 바로 인스턴스를 생성 가능하고, 생셩된 인스턴스 파일은 다른 컴퓨트 노드에서 잘 보입니다.

### live migration

Live Migration을 하려면 우선 [OpenStack에 있는데로 설정][3]{.broken_link}합니다. 그런데 이 상태로 live migration을 하면 바로 아래처럼 오류가 발생합니다.

    2013-06-03 11:52:20.743 11492 ERROR nova.virt.libvirt.driver \[-\] \[instance: 4717b4d4-61a7-4f4d-b5fc-5ff526822e89\] Live Migration failure: Unsafe migration: Migration may lead to data corruption if disks use cache != none  

이것은 libvirt가 live migration을 할 때 안정성을 이유로 디스크 캐쉬가 된 상태에서는 live migration을 수행하지 않습니다. nova.conf에서 disk_modes=file=none,block=none 형태로 캐쉬를 안해주면 되겠지만, 이렇게 하고 하면 인스턴스를 생성할 때 부터 문제가 생깁니다. ㅎㅎ

그래서 불안정하게라도 live migration 하겠다고 하면 디스크가 캐쉬가 된 상태도 라이브 마이그레이션을 지원합니다. 이를 설정하면 nova.conf에 아래처럼 설정합니다.

    live_migration_flag=VIR_MIGRATE_UNDEFINE_SOURCE, \  
        VIR_MIGRATE_PEER2PEER, \  
        VIR_MIGRATE_LIVE, \  
        VIR_MIGRATE_UNSAFE  

마지막의 VIR\_MIGRATE\_UNSAFE가 그것이죠.

어렇게 하고 live migration을 하면 잘 되는 것이죠.

    root@control:~# nova show cirros1  
    +-------------------------------------+------------------------------------------------------------+  
    | Property | Value |  
    +-------------------------------------+------------------------------------------------------------+  
    | status | ACTIVE |  
    | updated | 2013-06-03T07:17:53Z |  
    | OS-EXT-STS:task_state | None |  
    | OS-EXT-SRV-ATTR:host | compute01 |  
    | key\_name | admin\_key |  
    | image | cirros-0.3.1-x86_64 (f81518a9-9dc2-4a2a-aeaf-331cc074d063) |  
    | hostId | a2b92d1a2237ab4772f395b2fde3e462bfad9420e0ffc33ad3d1a19d |  
    | OS-EXT-STS:vm_state | active |  
    | OS-EXT-SRV-ATTR:instance_name | instance-00000011 |  
    | OS-EXT-SRV-ATTR:hypervisor_hostname | compute01.stack |  
    | flavor | m1.tiny (1) |  
    | id | 2991ad7d-b33e-4d3c-828b-da07338119f1 |  
    | security_groups | [{u'name': u'default'}] |  
    | user_id | 8f5b56a6a51c45338b0c1ba7cd7d9ebb |  
    | name | cirros1 |  
    | created | 2013-06-03T06:21:27Z |  
    | tenant_id | b5db61c3fa3845848b0f15c010dd500c |  
    | OS-DCF:diskConfig | MANUAL |  
    | metadata | {} |  
    | admin network | 10.250.0.2 |  
    | accessIPv4 | |  
    | accessIPv6 | |  
    | progress | 0 |  
    | OS-EXT-STS:power_state | 1 |  
    | OS-EXT-AZ:availability_zone | nova |  
    | config_drive | |  
    +-------------------------------------+------------------------------------------------------------+  

cirros1은 compute01에 있습니다. 이를 compute02로 옮긴다면..

    root@control:~# nova live-migration cirros1 compute02  

이제 compute02로 이사갔어요...

    root@control:~# nova show cirros1  
    +-------------------------------------+------------------------------------------------------------+  
    | Property | Value |  
    +-------------------------------------+------------------------------------------------------------+  
    | status | MIGRATING |  
    | updated | 2013-06-03T07:18:59Z |  
    | OS-EXT-STS:task_state | migrating |  
    | OS-EXT-SRV-ATTR:host | compute01 |  
    | key\_name | admin\_key |  
    | image | cirros-0.3.1-x86_64 (f81518a9-9dc2-4a2a-aeaf-331cc074d063) |  
    | hostId | a2b92d1a2237ab4772f395b2fde3e462bfad9420e0ffc33ad3d1a19d |  
    | OS-EXT-STS:vm_state | active |  
    | OS-EXT-SRV-ATTR:instance_name | instance-00000011 |  
    | OS-EXT-SRV-ATTR:hypervisor_hostname | compute01.stack |  
    | flavor | m1.tiny (1) |  
    | id | 2991ad7d-b33e-4d3c-828b-da07338119f1 |  
    | security_groups | [{u'name': u'default'}] |  
    | user_id | 8f5b56a6a51c45338b0c1ba7cd7d9ebb |  
    | name | cirros1 |  
    | created | 2013-06-03T06:21:27Z |  
    | tenant_id | b5db61c3fa3845848b0f15c010dd500c |  
    | OS-DCF:diskConfig | MANUAL |  
    | metadata | {} |  
    | accessIPv4 | |  
    | accessIPv6 | |  
    | admin network | 10.250.0.2 |  
    | OS-EXT-STS:power_state | 1 |  
    | OS-EXT-AZ:availability_zone | nova |  
    | config_drive | |  
    +-------------------------------------+------------------------------------------------------------+  

Update: Andrew님의 제보에 의하면 3.2 버전에서는 파일 싱크할 때 파일이 read only로 걸려서 instance도 read lock이 걸리는 현상이 있다는군요. 3.3 부터는 OK</pre>

 [1]: http://www.gluster.org/2013/05/glusterfs-is-ready-for-openstack/
 [2]: http://openstack.redhat.com
 [3]: http://docs.openstack.org/trunk/openstack-compute/admin/content/configuring-migrations.html