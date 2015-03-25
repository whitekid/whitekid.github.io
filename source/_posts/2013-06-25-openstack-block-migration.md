---
id: 1323
title: 'OpenStack: block migration'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1323
permalink: /archives/1323
dsq_thread_id:
  - 1433813480
tags:
  - migration
  - OpenStack
---
잘 알고 있다시피.. live migration을 하려면 shared storage(NFS, DFS...)가 필요합니다. 근데 share storage는 필연적으로 local storage에 비해서 느릴 수 밖에 없습니다. 그렇지요.

그리고 live migration은 OpenStack 시스템 유지/ 보수를 위해서도 필요합니다. 호스트 머신의 업그레이드가 필요할 때, 호스트 머신에 뭔가 작업을 할 때, 해당 호스트에서 서비스되는 인스턴스를 다른 곳으로 옮기고 작업해야하니깐요...

위에 언급했다시피, live migration은 shared storage가 필요하고, 이를 위해서는 IO를 일정부분 포기해야하는 경우가 생겨서, 이를 어떻게 만회할까하고 여기저기 뒤져보다가... block migration 기능이 있다는 것을 알았습니다.

live migration은 다른 호스트로 메모리만 복사하는 반면에 block migration은 메모리와 블럭 디바이스(인스턴스 스토리지, 볼륨 스토리지)도 같이 대상 호스트로 복사합니다. 결국 live migration이 조금 느린 반면에 shared storage가 필요 없어서, 유지보수를 위한 migration에는 사용할 수 있을 것 같습니다. 물론 갑작스런 호스트 머신의 오류일 경우에는 해당 사항이 없겠습니다.

사용방법은 간단합니다. 기존의 live migration 명령에 -block_migration 옵션만 붙이면 됩니다. 즉, 아래와 같죠.

    $ nova live-migration -block_migrate

이를 위한 OpenStack 설정은 [live migration 문서처럼 설정][1]하고, 추가로 /etc/nova/nova.conf에 아래처럼 block\_migration\_flag를 설정합니다.

    block_migration_flag= \
        VIR_MIGRATE_UNDEFINE_SOURCE, \
        VIR_MIGRATE_PEER2PEER, \
        VIR_MIGRATE_NON_SHARED_INC, \
        VIR_MIGRATE_LIVE

별 문제 없으면 잘 될겁니다. live migration이 필요했지만, shared storage가 준비되지 않으신 분들.. 한번 써 보세요~

그리고 추가로 OpenStack에서 migration에 대해서 간단히 정리합니다.

### (True) live migration

shared storage를 사용하는 경우, instance의 memory만 대상 호스트로 복사하여 live migration을 수행합니다. 당연히 shared storage(NFS, GlusterFS, CephFS...)가 필요합니다.

메모리만 복사하므로 수초내에 migration이 완료됩니다.

    $ nova live-migration <instance> <dest host>

참고) shared storage를 사용할 경우, live migration을 사용하기 위해서 disk cache를 disable 해야합니다. file 또는 ceph를 사용할 경우는 이 설정이 자동으로 들어가지만,  GlusterFS를 instance directory에 mount해서 사용할 경우 disk cache를 disable하는 것이 동작하지 않습니다. 따라서 glusterfs를 사용할 경우는 live\_migration\_flag를 아래처럼 VIR\_MIGRATE\_UNSAFE를 추가합니다.

    live_migration_flag = \
        VIR_MIGRATE_UNDEFINE_SOURCE, \
        VIR_MIGRATE_PEER2PEER, \
        VIR_MIGRATE_LIVE, \
        VIR_MIGRATE_UNSAFE

### block migration

live migration에 추가로 스토리지도 복사합니다. 약간 느리지만 live migration의 장점과 local storage의 IO의 장점을 동시에 취할 수 있습니다. 그리고 qemu에서 제공되는 기능을 사용하기 때문에 별도의 설정은 필요 없습니다.

    $ nova live-migration -block_migrate <instance> <dest host>

### migration

이건 live migration이 아니고, instance shutdown -> disk image copy -> instance boot 과정을 거침니다. 당연히 해당 호스트는 리부팅 됩니다. 그리고 migration이 되는 호스트는 scheduler에 의해서 자동으로 선택됩니다

    $ nova migrate <instance>

block migration은 다른 호스트로 복사하는 기능을 qemu의 기능을 사용하므로 별도의 설정이 필요없었지만, migrate는 qemu나 libvirt의 기능을 사용하지 않고 수동(^^)으로 rsync+ssh를 이용하여 복사하기 때문에, 이동하려는 nova compute 노드에 nova 유저로 ssh login이 아무런 장애없이 되야됩니다. 또한 ubuntu로 설치했다면 nova 계정의 login shell이 /bin/false로 되어있는데 /bin/bash로 수정합니다. 결과적으로 compute에서 다른 compute로 아래 명령을 수행했을 때 오류가 없어야 합니다(아직까지 문서에 없음^^).

    root@compute01:~# sudo -u nova ssh compute02 hostname
    compute02

  * ssh public key authentication
  * ssh host key 등록

ps. 예상대로라면 volume을 붙인 인스턴스에서는 볼륨은 굳이 복사할 필요가 없으므로 volume이 없는 인스턴스와 block migration 성능 차이가 없어야 하는데도, 이상하게 volume이 붙어있는 인스턴스는 느리군요.. 이거 왜 이럴까요?

ps. 약간 알콜이 들어간 상태에서 쓴 글이라... 오타 작렬.. 논지도 왔다갔다... ㅎㅎ

 [1]: http://docs.openstack.org/trunk/openstack-compute/admin/content/configuring-live-migrations.html