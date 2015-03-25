---
id: 1312
title: keystone token이 너무 많아요..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1312
permalink: /archives/1312
dsq_thread_id:
  - 1406595535
categories:
  - Uncategorized
tags:
  - keystone
  - OpenStack
  - token
---
지난 일주일간 회사 워크삽으로 자리를 비웠다. 그 동안 단 한번도 노트북을 켜지 않았으니, 이거 참 대대한 일인 듯 하다. 작년 휴가에도 중간에 콜 받아서 노트북 켠 경험이 있는데 말이다...

각설하고.. 어쨌든 오늘 출근해서, 그래 내가 뭘 하고 있었던 거지? 하면서 설치된 오픈스택 구성을 둘러보려고, 딸랑 nova list 명령을 치는데, 이녀석이 먹통이다... 아무런 에러로 안뱉어내고 그냥 조용이 가만히 있는다.

이전에도 비슷하나 경험이 있었지만, 그때는 대충 넘어갔었지만, 들은 것도 있어서 뒤져봤는데 역시나... keystone의 token table에 데이터가 많이 쌓었나 보다.

    mysql> select count(*) from token;  
    +----------+  
    | count(*) |  
    +----------+  
    |    20916 |  
    +----------+  
    1 row in set (25.17 sec)  

뭐.. 개인 테스트 환경이고 해서, 무식하게 다 지우고 하니 nova list 명령이 잘 된다. 대략  들은 적이 있다. 각 호스트들의 agent들이 api를 수행할 때, 모두 keystone 인증 토큰을 발급 받는데, 이 토큰들이 계속 누적되어서 문제를 발생하는 것이다. 우선 간단하게 expire된 token을 날려버리는 것도 좋겠다.

    mysql> delete from token where expires < current_timestamp [/code]

근데 저 정도의 숫자로 MySQL의 성능 이슈에 걸리는 것도 이상한 것이다. MySQL 튜닝 문제인 것 같다. token을 보면 primary key를 지외하고는 인덱스를 잡지 않았으며, 쿼리에 맞게 인덱스를 추가하면 되겠다.

    # Query_time: 77.709733 Lock_time: 0.000087 Rows_sent: 0 Rows_examined: 20919 use keystone; SET timestamp=1371436405; SELECT token.id AS token_id, token.expires AS token_expires, token.extra AS token_extra, token.valid AS token_valid, token.user_id AS token_user_id, token.trust_id AS token_trust_id FROM token WHERE token.expires > '2013-06-17 02:32:08&' AND token.valid = 0;  

그냥 무식하게 DELETE 하기 전의 token 테이블에 걸린 slow query를 보면 위와 같은데... expires, valid에 index를 걸어주면 좀 더 빠른 실행이 가능하지 않을까?

    mysql> explain select id from token where expires > '2013-06-17 02:32:08' AND token.valid = 0;
    +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
    | id | select_type | table | type | possible_keys | key  | key_len | ref  | rows | Extra       |
    +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
    |  1 | SIMPLE      | token | ALL  | NULL          | NULL | NULL    | NULL |  492 | Using where |
    +----+-------------+-------+------+---------------+------+---------+------+------+-------------+
    1 row in set (0.00 sec)
     
    mysql> create index idx_token_expires_valid on token(expires, valid);
    Query OK, 0 rows affected (0.09 sec)
    Records: 0  Duplicates: 0  Warnings: 0
     
    mysql> explain select id from token where expires > '2013-06-17 02:32:08' AND token.valid = 0;
    +----+-------------+-------+-------+-------------------------+-------------------------+---------+------+------+--------------------------+
    | id | select_type | table | type  | possible_keys           | key                     | key_len | ref  | rows | Extra                    |
    +----+-------------+-------+-------+-------------------------+-------------------------+---------+------+------+--------------------------+
    |  1 | SIMPLE      | token | index | idx_token_expires_valid | idx_token_expires_valid | 10      | NULL |  492 | Using where; Using index |
    +----+-------------+-------+-------+-------------------------+-------------------------+---------+------+------+--------------------------+
    1 row in set (0.00 sec)

explain에 보듯이, 해당 slow query는 index를 타고 있다. 이제 일주일 후에도 같은 증상이 발생하는지 확인해보자..

추가) 그리고 어떤 녀석이 이런 token을 무지막지하게 만들어 내는지 확인해볼 필요가 있다.

    mysql> select user_id, count(*) from token group by user_id;
    +----------------------------------+----------+
    | user_id                          | count(*) |
    +----------------------------------+----------+
    | 36068019a00e427c8c8e97e8a3cee8a2 |        1 |
    | 5700bfc6b03b476f97021e5eede7989e |        4 |
    | 8232fea985ff44c0be44b0473cac5fac |      557 |
    +----------------------------------+----------+

위 처럼 특정 user_id에 의해서 집중적으로 토큰이 발생하는 것을 확인할 수 있으며

    mysql> select id, name from user;
    +----------------------------------+---------+
    | id                               | name    |
    +----------------------------------+---------+
    | 5700bfc6b03b476f97021e5eede7989e | admin   |
    | f09f583150f24c72a3a3ef1e1b12ae7d | cinder  |
    | 8152fd3ea78a477eb096e829aa2e0bce | demo    |
    | dcabff792209441794a4256f6611f43e | glance  |
    | 36068019a00e427c8c8e97e8a3cee8a2 | nova    |
    | 8232fea985ff44c0be44b0473cac5fac | quantum |
    +----------------------------------+---------+
    6 rows in set (0.00 sec)

처럼 quantum 서비스에 의해서 발생됩니다. quantum의 plugin 들이지요.

update 1) 저 무지막지한 토큰은 quantum에 의해 발생하는 것이 아니라, nova 서비스에서 quantum을 사용하면서 keystone token을 재사용하지 않아서 발생하는 문제입니다. 소스는 잊어버렸...

update 2) [Launchpad에 버그가 등록][1]되어 있습니다.

update 3) 이 문제를 해결하는 가장 간단한 방법은 token driver를 memcache로 변경하는 것이다.  

    [token]  
    driver = keystone.token.backends.memcache.Token  

 [1]: https://bugs.launchpad.net/ubuntu/+source/keystone/+bug/1032633