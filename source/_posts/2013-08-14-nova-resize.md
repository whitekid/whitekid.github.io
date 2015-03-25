---
id: 1389
title: nova resize
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1389
permalink: /archives/1389
dsq_thread_id:
  - 1605720032
categories:
  - Uncategorized
tags:
  - nova
  - OpenStack
  - resize
---
nova resize는 메뉴얼에 나와있는 것 같이, 인스턴스에 적용된 flavor를 바꾸고, 이에 따라서 서버의 설정을 변경하는 명령입니다. 이의 사용 방법은 간단합니다.

    $ nova resize -poll <instance id> <flavor id>  
    $ nova resize-confirm <instance id>  

아주 간단하게 보이죠... 근데 이렇다면 제가 이 글을 쓸 이유가 없습니다. 메뉴얼에 나와있지 않은 숨은 복병이 있습니다.

아무 생각없이 단순 무식하게 이 명령을 수행하면 아마 대부분 에러가 발생한 것입니다.

    $ nova resize -poll my-instance 3

    Instance resizing... 0% complete  
    Error resizing instance  

음.. 이 상태에서는 인스턴스는 종료되어 접근할 수 없고, nova list 명령으로 보면 아래처럼 에러가 났다고 그러고, 재부팅 하기 위해서 nova reboot를 하면 에러 상태에서는 해당 명령을 수행할 수 없다고 합니다.

    $ nova list
    +--------------------------------------+--------------+--------+------------------------------+
    | ID                                   | Name         | Status | Networks                     |
    +--------------------------------------+--------------+--------+------------------------------+
    | cb0802f6-0946-41d7-a7e6-6280974686e0 | my-instance  | ERROR  | admin=10.10.10.2, 10.250.0.4 |
    +--------------------------------------+--------------+--------+------------------------------+
   
    $ nova reboot --hard my-instance
    ERROR: Cannot 'reboot' while instance is in vm_state error (HTTP 409) (Request-ID: req-b745b011-961d-4b11-9366-a85cbe027fc8)

## recover error state

우선 resize를 제대로 동작하게 하는 것 보다, 이 에러 상태를 해결하는 것이 더 중요해졌습니다. 최악의 경우 DB를 수정하는 방법을 생각할지도 모르나.. 생각보다 간단합니다. 우선 쉼호흡을 하고..

    $ nova reset-state --active my-instance
    $ nova list
    +--------------------------------------+--------------+--------+------------------------------+
    | ID                                   | Name         | Status | Networks                     |
    +--------------------------------------+--------------+--------+------------------------------+
    | cb0802f6-0946-41d7-a7e6-6280974686e0 | my-instance  | ACTIVE | admin=10.10.10.2, 10.250.0.4 |
    +--------------------------------------+--------------+--------+------------------------------+

ACTIVE 상태이니... 인스턴스 접근을 해보려 하겠지만 안됩니다. 데이터베이스 상에서 status만 reset된 상태입니다. 아직은 인스턴스는 그대로 죽어있습니다. 이제 인스턴스를 상리기 위해서 reboot 합니다.

    $ nova reboot -hard my-instance  

이제 잠시 기다리면 인스턴스가 올라옵니다.

## resize는 어떻게 동작하는가?

그럼 resize는 어떻게 동작하는지에 대해서 보면, 위에서 에러가 발생했을 때 상황에서 확인할 수 있습니다.

    $ nova show my-instance
    +----------------------+-----------------------------------------------------------------------------------------------------------------+
    | Property             | Value                                                                                                          |
    +----------------------+-----------------------------------------------------------------------------------------------------------------+
    | status               | ERROR                                                                                                           |
    | OS-EXT-STS:vm_state  | error                                                                                                           |
    | fault                | {u'message': u'ProcessExecutionError', u'code': 500, u'details': u'Unexpected error while running command.      |
    |                      | Command: ssh 10.100.0.32 mkdir -p /var/lib/nova/instances/cb0802f6-0946-41d7-a7e6-6280974686e0                  |
    |                      | Exit code: 255                                                                                                  |
    |                      | Stdout: \'\'                                                                                                    |
    |                      | Stderr: \'Host key verification failed.\\r\                                                                     |
    |                      | \'                                                                                                              |
    |                      |   File "/usr/lib/python2.7/dist-packages/nova/compute/manager.py", line 224, in decorated_function              |
    |                      |     return function(self, context, *args, **kwargs)                                                             |
    |                      |   File "/usr/lib/python2.7/dist-packages/nova/compute/manager.py", line 2373, in resize_instance                |
    |                      |     block_device_info)                                                                                          |
    |                      |   File "/usr/lib/python2.7/dist-packages/nova/virt/libvirt/driver.py", line 3480, in migrate_disk_and_power_off |
    |                      |     inst_base_resize)                                                                                           |
    |                      |   File "/usr/lib/python2.7/contextlib.py", line 24, in __exit__                                                 |
    |                      |     self.gen.next()                                                                                             |
    |                      |   File "/usr/lib/python2.7/dist-packages/nova/virt/libvirt/driver.py", line 3457, in migrate_disk_and_power_off |
    |                      |     utils.execute(\'ssh\', dest, \'mkdir\', \'-p\', inst_base)                                                  |
    |                      |   File "/usr/lib/python2.7/dist-packages/nova/utils.py", line 239, in execute                                   |
    |                      |     cmd=\' \'.join(cmd))                                                                                        |
    |                      | ', u'created': u'2013-08-14T09:30:00Z'}                                                                         |
    +----------------------+-----------------------------------------------------------------------------------------------------------------+


보면, 에러가 발생했을 때 traceback이 보이고, 여기서 관심을 둘 부분은 아래입니다.

    Command: ssh 10.100.0.32 mkdir -p /var/lib/nova/instances/cb0802f6-0946-41d7-a7e6-6280974686e0  

다른 호스트로 ssh로 디렉토리를 만들면서 명령을 실행하다가 발생합니다. 어디선가 본 적이 있을 겁니다. 녜.. 이 내용은 이전에 [migration에 대한 포스트][1]에서 migration에서 실행하는 명령과 같습니다. 즉.. resize는 ssh를 통해서 다른 compute에 디스크를 복사하고, 이는 migration의 필요 요구사항과 같습니다.

정리하면 migration의 요구사항은 다음과 같습니다. 이게 resize의 요구사항과 같습니다.

  * nova user로 다른 compute node에 ssh로 인증없이 로그인 가능(public key authentication)
  * 사용가능한 shell(/bin/bash) 지정: ubuntu 패키지는 /bin/false로 설정하고 있음.
  * known_hosts에 대상 compute 호스트 등록

## resize가 완료 되었군요

resize한 이후에 보면, 메모리, vcpu등의 리소스는 큰 문제가 없습니다. 다만 root partition의 경우는 디스크가 더 커질 경우, root partition 다음에 빈 영역으로 남아있습니다.

cloud-init에 의해서 root partition이 자동으로 될 것입니다. 물론 ubuntu의 경우 입니다. CentOS는 안됩니다. CentOS가 안되는 건 좀 복잡한 문제인데.. 그건 나중에...

## 정리

  * resize는 migrate와 유사합니다. shutdown -> ssh + rsync 복사 -> reboot
  * root partition은 Ubuntu의 경우 사이즈 조절이 자동으로 됩니다.

 [1]: http://blog.woosum.net/archives/1323 "OpenStack: block migration"