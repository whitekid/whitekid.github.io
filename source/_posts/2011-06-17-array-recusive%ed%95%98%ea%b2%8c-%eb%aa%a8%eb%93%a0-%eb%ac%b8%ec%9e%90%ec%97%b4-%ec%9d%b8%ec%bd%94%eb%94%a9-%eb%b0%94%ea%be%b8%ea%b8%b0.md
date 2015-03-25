---
id: 715
title: array recusive하게 모든 문자열 인코딩 바꾸기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=715
permalink: /archives/715
dsq_thread_id:
  - 732113343
categories:
  - Uncategorized
tags:
  - php
---
요즘 json API를 작업하는데, 문제는 내부 문자셋이 euc-kr다. 그래서 결과를 모두 받아서 array 통체로 문자열 인코딩을 변경할 필요가 있어서 간단하게 해봤음.~

[code lang="php"]  
public function conv($array){  
$output = array();  
$param = array(&$this, &$output);

array_walk($array, function($v, $k, $param){  
if(is_array($v)){  
$v = $param[0]->conv($v);  
} else if (is_string($v)){  
$v = iconv('euckr', 'utf8&', $v);  
}

$param\[1\]\[$k\] = $v;  
}, &$param);  
return $output;  
}

function __destruct(){  
echo json_encode($this->conv($this->resp));  
}  
}  
[/code]