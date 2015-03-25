---
id: 599
title: TDBXMetaDatabaseColumnNames.SupportsParameterMetadata
author: whitekid
layout: post
guid: http://www.woosum.net/?p=599
permalink: /archives/599
dsq_thread_id:
  - 948545208
categories:
  - Uncategorized
tags:
  - pgdbx4
---
pgdbx4를 Delphi XE로 돌려봤습니다. 그런데 "Invalid Ordinal" 에러가 튀어나오는 군요. 이런 전혀 유추할 수 없는 에러메시지 싫습니다.

하여간 찾아보니 TDBXMetaDatabaseColumnNames.SupportsParameterMetadata 가 추가되었군요. 간단하게 넣어서 이 작업은 완료!!

그러나 테스트 케이스가 하나 오류나는 것이 있어서 그것 확인하고 다시 릴리스 하렵니다.