---
id: 82
title: pager_query 없이 theme_pager 사용하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=82
permalink: /archives/82
dsq_thread_id:
  - 759827296
tags:
  - drupal
  - pager
---
drupal에서 쿼리 결과를 pager로 보려면 page_query로 하면되는데, 쿼리결과가 아닌 다른건.. 저의 경우는 별도의 검색 서버를 두고 거기서 가져오는 형태는 지원하지 않습니다. 그래서 theme_pager 함수를 살펴봤더니.. 아래처럼 하면 되는군요.

1. 먼저 theme_page에서 pager에 사용하는 전역변수를 선언합니다. 여기다가 적당히 데이터를 넣어주면 되는 것이지요.
```
    global $pager_total, $pager_page_array;
```
2. 전체 페이지수 설정, $total_results는 전체 검색 결과 수이고 get_page_size()는 한 페이지당 표시할 데이터 수입니다.
```
    $pager_total[0] = ceil($total_results / get_page_size());
```
3. 표시될 페이지 설정, $page는 현재 표시될 페이지 입니다.
```
    $pager_page_array = array();
    $page_count = ceil($total_results / get_page_size());
    for($i=0; $i<$page_count; $i++) array_push($pager_page_array, $i + $page);
```
4. 이제 pager 사용..
```
    theme('pager', get_page_size(), $total_results);
```
5. 당연히 검색하는 부분에서 파라미터로 넘어간 페이지수에 맞게 검색하면 됩니다.