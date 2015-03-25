---
id: 1417
title: quantum lbaas
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1417
permalink: /archives/1417
dsq_thread_id:
  - 2043606837
categories:
  - Uncategorized
tags:
  - LBaaS
  - OpenStack
  - Quantum
---
create pool

    $ NET_ID=e5768970-4e96-467d-9be1-e0e0b789f025
    $ quantum lb-pool-create --name test --lb-method \
      ROUND_ROBIN --protocol TCP --subnet-id=$NET_ID
     
    quantum lb-pool-list
    +--------------------------------------+------+-------------+----------+----------------+----------------+
    | id                                   | name | lb_method   | protocol | admin_state_up | status         |
    +--------------------------------------+------+-------------+----------+----------------+----------------+
    | 044208a8-9e29-4fee-b41b-9b58567f3874 | test | ROUND_ROBIN | TCP      | True           | PENDING_CREATE |
    +--------------------------------------+------+-------------+----------+----------------+----------------+
    
create vip

    $ quantum lb-vip-create --name test-vip1 --protocol-port 80 --protocol TCP --subnet-id=$NET_ID test
    $ quantum lb-vip-list
    +--------------------------------------+-----------+-------------+----------+----------------+--------+
    | id                                   | name      | address     | protocol | admin_state_up | status |
    +--------------------------------------+-----------+-------------+----------+----------------+--------+
    | 515fa2c5-a135-4b59-b247-803e844383f1 | test-vip1 | 10.10.100.5 | TCP      | True           | ACTIVE |
    +--------------------------------------+-----------+-------------+----------+----------------+--------+

여기서 pool도 status가 ACTIVE로 바뀌고.. lbaas가 동작하는 노드에 qlbaas- 로 ip namespace가 생긴다.

생성된 vip에... 다른 port로 만들면 오류가 발생

    $ quantum lb-vip-create -name test-vip-https -protocol-port 443 -protocol TCP -subnet-id=$NET_ID test -address=10.10.100.5  
    Another Vip already exists for pool 044208a8-9e29-4fee-b41b-9b58567f3874  