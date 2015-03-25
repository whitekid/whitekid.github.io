---
id: 138
title: nginx가 꽤 괜찮은가 본데?
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=138
permalink: /archives/138
dsq_thread_id:
  - 866547387
tags:
  - nginx
  - rails
---
[RubyOnRails][1]를 해볼 일이 생겼습니다. 기회가 없어서 하지않았던 건데.. 어쩌다보니 이것도 돌아보게 되었네요.

여기저기 [문서][2]를 뒤지다보다 보니 [nginx][3]에 대한 언급이 되고있고 특히 nginx에 대해서 눈에 띄는건 성능이 굉장이 좋다는 군요. 홈페이지 [about][4]에 소개된 [FastFail.FM][5], [WordPress.com][6]의 예를 둘러보니 시도해볼까? 하는 생각이 듭니다.

Apache를 현재 무리없이 쓰고있지만, 어느 순간부턴 느낌이 너무 복잡해졌다고나할까? 하여간 그런 느낌이 들고, 요즘은 웹 서버에서는 연결받고 Application server로 바로 넘기는 일 밖에 안하나 이런 일에 충실한 웹 서버면 충분 하겠지요.

시간나면 해봐야죠. ㅋㅋ

 [1]: http://rubyonrails.org/
 [2]: http://wiki.rubyonrails.org/deployment/nginx-mongrel
 [3]: http://nginx.org
 [4]: http://nginx.org/en/
 [5]: http://blog.fastmail.fm/2007/01/04/webimappop-frontend-proxies-changed-to-nginx/
 [6]: http://barry.wordpress.com/2008/04/28/load-balancer-update/