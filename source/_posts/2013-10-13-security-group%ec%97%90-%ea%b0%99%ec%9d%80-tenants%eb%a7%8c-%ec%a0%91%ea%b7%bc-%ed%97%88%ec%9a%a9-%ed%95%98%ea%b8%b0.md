---
id: 1413
title: 'security group: 인스턴스 그룹에 접근 허용하기'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1413
permalink: /archives/1413
dsq_thread_id:
  - 1849911786
categories:
  - Uncategorized
tags:
  - OpenStack
  - SecurityGroup
---
[SecurityGroup][1]은 인스턴스에 연결된 포트에 허용되는 트래픽을 제어하는 기능입니다.

처음 오픈스택 인스턴스 프로젝트를 할당받고 가장 먼저 하는 것이 22번 포트에 대한 접근을 허용하는 것이니.. 대부분 잘 알고 있을 겁니다.

그런데 기본으로 제공되는 default security group만 운영하다 보면(아마 대부분 그럴지도) 하나의 의문점이 생깁니다.

아래처럼 인스턴스가 있다고 가정했을 때

  * web
  * mysql

web 인스턴스는 80번 포트, mysql 포트는 3306 포트가 필요합니다. 그래서 별 생각없이 default security group에 80, 3306 포트를 열어줍니다. 하지만 여기서 생각해볼 것이..

  * web에서는 3306 포트를 열 필요가 없습니다.
  * mysql에서는 80번 포트를 열 필요가 없습니다. 그리고 db 접근은 web에서만 하니 web에서 접근하는 트래픽만 허용합니다.

첫번째 경우는 큰 문제가 없습니다.  단순히 80번 포트만 여는 security group을 만들고 web 인스턴스에 적용하면 되니깐요.

    $ nova secgroup-create web 'allow web access'
    +--------------------------------------+------+------------------+
    | Id                                   | Name | Description      |
    +--------------------------------------+------+------------------+
    | d60f5be1-8a60-4523-ba4e-3230edf5b444 | web  | allow web access |
    +--------------------------------------+------+------------------+
    $ nova secgroup-add-rule web tcp 80 80 0.0.0/0
    +-------------+-----------+---------+----------+--------------+
    | IP Protocol | From Port | To Port | IP Range | Source Group |
    +-------------+-----------+---------+----------+--------------+
    | tcp         | 80        | 80      | 0.0.0/0  |              |
    +-------------+-----------+---------+----------+--------------+
    $ nova add-secgroup web web
    $ nova show web
    $ nova show charlie-web | grep security
    | security_groups             | [{u'name': u'default'}, {u'name': u'web'}]                     |

자.. 이제 MySQL 인스턴스에 Security Group을 적용해야하는데... web에만 적용하면 되니깐.. security group을 만들려고 보는데..

    $ nova secgroup-create mysql 'allow mysql access'
    +--------------------------------------+-------+--------------------+
    | Id                                   | Name  | Description        |
    +--------------------------------------+-------+--------------------+
    | 7d197716-501f-46fa-a6fb-bd4e0c9dd5bb | mysql | allow mysql access |
    +--------------------------------------+-------+--------------------+

이제 rule을 추가하려는데.. web에서만 접근을 하용하는 것이니깐.. web 인스턴스 ip만 cidr로 허용합니다.

    $ nova secgroup-add-rule mysql tcp 3306 3306 172.16.202.214/32
    +-------------+-----------+---------+-------------------+--------------+
    | IP Protocol | From Port | To Port | IP Range          | Source Group |
    +-------------+-----------+---------+-------------------+--------------+
    | tcp         | 3306      | 3306    | 172.16.202.214/32 |              |
    +-------------+-----------+---------+-------------------+--------------+

문제없나요? 근데.. 뭔가 이상하지 않나요? 만일 web 인스턴스가 늘어난다면? 새로운 인스턴스에 대한 rule을 계속 추가해야될까요?.. 만일 그래야된다면... 이거 Security Group이 참 써 먹을 것이 못되는 것이 됩니다.

역시나 예상하다시피 tenant 인스턴스 group에 대해서 traffic을 제어하는 기능이 있습니다. rule을 추가된 후에 나오는 source group이라는 것이 키워드입니다. nova에도 해당 명령이 있고.. 이에 대한 설명은 아래처럼 굉장히 불친절하게 되어있습니다.

> secgroup-add-group-rule Add a source group rule to a security group.

설명을 봐도 무슨 말인지 쉽게 알 수 없습니다. 하지만 내용은 간단한 것으로 특정 cidr의 rule을 주는 것이 아니라 주어진 security group이 적용된 인스턴스에 대해서 secutiry group을 적용한다는 이이기 입니다.

즉 mysql 접근을 web에서만 접근하려고 하고, web 인스턴스는 모두 web security group이 적용되어 있다고 한다면

    $ nova secgroup-add-group-rule mysql web tcp 3306 3306
    +-------------+-----------+---------+----------+--------------+
    | IP Protocol | From Port | To Port | IP Range | Source Group |
    +-------------+-----------+---------+----------+--------------+
    | tcp         | 3306      | 3306    |          | web          |
    +-------------+-----------+---------+----------+--------------+

source group을 web으로 지정하여 web 인스턴스에 대해서 mysql port에 대한 접근을 허용할 수 있습니다. 여기서 다른 인스턴스에 web security group을 적용하면 그 인스턴스도 바로 mysql에 접근이 가능합니다(물리 노드에서 보면 해당 인스턴스의 ip address가 iptables에 바로 추가됩니다). 물론 다른 녀석들은 mysql 포트에 접근이 제한되구요.

그러므로 만일 source group을 default로 한다면 해당 tenant의 모든 인스턴스가 mysql에 접근할 수 있습니다

 [1]: http://docs.openstack.org/network-admin/admin/content/securitygroups.html