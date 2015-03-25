---
id: 101
title: Bugzilla 데이터베이스를 MySQL에서 PostgreSQL로 이전하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=101
permalink: /archives/101
dsq_thread_id:
  - 756826531
categories:
  - Uncategorized
tags:
  - Bugzilla
---
그냥 contrib/bzdbcopy.pl을 사용하면 된다. 그냥하면 안되고 파일 열어서 데이터베이스 연결 속성을 좀 바꿔주면 된다.

그리고 Custom Field를 만들었다면 그건 여기서 처리 못한다. 그럴땐 간단한 스크립트 만들어 돌리면 된다.

    import MySQLdb  
    import psycopg2
    
    mysql_bugs_conn = MySQLdb.connect(user='*\*\\*\*', db='\*\*\*\*', passwd='\*\***')  
    pgsql_bugs_conn = psycopg2.connect('dbname=*\*\\*\* user=\*\***')
    
    mysql_cursor = mysql_bugs_conn.cursor()  
    pgsql_cursor = pgsql_bugs_conn.cursor()
    
    mysql_cursor.execute("SELECT bug_id, cf_testcase FROM bugs WHERE cf_testcase is not null")  
    for bug_id, cf_testcase in mysql_cursor.fetchall():  
    pgsql_cursor.execute("UPDATE bugs SET cf_testcase = %s WHERE bug_id = %s", (cf_testcase, bug_id))  
    pgsql_bugs_conn.commit()  

그런데 이걸 왜 했냐고? MoinMoin에서 Bugzilla 데이터를 보여주는 Macro를 사용하는데 이게 가끔은 ????게 나온다. 뭐 인코딩 문제인데... MySQL이 문젠가 하고 그냥 바꿔봤다. 근데 잘 된다. 어짜피 MySQL보단 PostgreSQL을 더 선호하니 그냥 해버렸다. ^^;

추가: PostgreSQL로 이전하고 pgpool로 connection pool을 돌렸다. ㅋㅋ