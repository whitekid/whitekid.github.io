---
id: 545
title: 'pgdbx4 0.4 Release: dbExpress driver for PostgreSQL'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=545
permalink: /archives/545
dsq_thread_id:
  - 875900222
categories:
  - Uncategorized
tags:
  - dbExpress
  - Delphi
  - MadeByKidd
  - pgdbx4
  - PostgreSQL
---
그간 방치해뒀던 PostgreSQL용 dbExpress 드라이버를 오늘 좀 수정했습니다.

  * Boolean 형태가 잘 안가져오던 문제
  * BYTEA/TEXT 타입이 Next를 해도 다음 row를 가져오지 못하던 문제
  * 기타 기억안나는 자잘한 것...

자세한 내용은 [pgexp4 페이지][1]를 참고하세요.

 [1]: /pgdbx4