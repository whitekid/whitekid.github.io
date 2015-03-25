---
id: 911
title: nova-network floating ip
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=911
permalink: /archives/911
dsq_thread_id:
  - 733785755
categories:
  - Uncategorized
tags:
  - OpenStack
---
floating ip는 amazone의 elastic ip와 비슷한 기능으로, 할당받은 ip를 가상머신에 자유롭게 할당, 제거할 수 있다. 직접 NIC을 조절하는 것이 아닌 nova-network 노드에서 1:1 NAT으로 할당한다.

float ip 생성

<pre>$ nova-manage floating create --ip_range=10.200.3.0/24</pre>

만들어진 floating ip를 확인할 수 있다.

<pre>$ nova-manage floating list
None    10.200.3.1      None    nova    eth0
None    10.200.3.2      None    nova    eth0
None    10.200.3.3      None    nova    eth0
.....</pre>

floating ip를 하나 할당받는다.

<pre>$ nova floating-ip-create
+------------+-------------+----------+------+
|     Ip     | Instance Id | Fixed Ip | Pool |
+------------+-------------+----------+------+
| 10.200.3.1 | None        | None     | nova |
+------------+-------------+----------+------+
$ nova floating-ip-create
+------------+-------------+----------+------+
|     Ip     | Instance Id | Fixed Ip | Pool |
+------------+-------------+----------+------+
| 10.200.3.2 | None        | None     | nova |
+------------+-------------+----------+------+</pre>

할당받은 ip를 instance에 설정한다.

<pre>$ nova add-floating-ip 6618a76e-a1d3-4912-a71f-42dc3daf893b 10.200.3.2
$ nova-manage floating list
c99bd5af477d403e9421f6924836412a        10.200.3.1      None    nova    eth0
c99bd5af477d403e9421f6924836412a        10.200.3.2      6618a76e-a1d3-4912-a71f-42dc3daf893b    nova    eth0
None    10.200.3.3      None    nova    eth0
None    10.200.3.4      None    nova    eth0
.....</pre>

  * 10.200.3.1은 할당 받았지만, instance에 연결되지 않았다.
  * 10.200.3.2은 할당 받아서 6618a76e-a1d3-4912-a71f-42dc3daf893b에 연결되었다.

nova-network 노드에서 iptables를 확인해보면 아래와 같이 NAT이 설정된 것을 확인할 수 있다.

<pre>$ iptables -L -t nat
....

Chain nova-network-OUTPUT (1 references)
target     prot opt source               destination         
DNAT       all  --  anywhere             10.200.3.2           to:10.200.2.5

....

Chain nova-network-PREROUTING (1 references)
target     prot opt source               destination         
DNAT       tcp  --  anywhere             169.254.169.254      tcp dpt:http to:10.200.1.9:8775
DNAT       all  --  anywhere             10.200.3.2           to:10.200.2.5

.....

Chain nova-network-float-snat (1 references)
target     prot opt source               destination         
SNAT       all  --  10.200.2.5           anywhere             to:10.200.3.2

....</pre>

<http://wiki.woosum.net/nova#floating_ip>