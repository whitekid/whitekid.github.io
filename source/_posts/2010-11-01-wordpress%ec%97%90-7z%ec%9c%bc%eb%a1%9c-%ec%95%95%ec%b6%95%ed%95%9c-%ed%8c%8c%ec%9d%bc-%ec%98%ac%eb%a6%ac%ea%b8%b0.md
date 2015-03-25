---
id: 538
title: WordPress에 7z으로 압축한 파일 올리기..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=538
permalink: /archives/538
dsq_thread_id:
  - 762916942
tags:
  - WordPress
---
http://netweblogic.com/other/adding-removing-allowed-filetypes-wordpress-media-library/ 를 보시라. 이런 저런 복잡한 말들이 있는 것 같은데. 그냥 wp-includes/functions.php 파일을 열어서 get\_allowed\_mime_types 에 적당히 추가해주면 된다. 현재 이를 관리할 수 있는 Plugin은 없는 듯 하다.

    '7z|7zip' => 'application/7zip',