---
id: 407
title: iPhone에 custom root certificate 설치하기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=407
permalink: /archives/407
dsq_thread_id:
  - 717992703
categories:
  - Uncategorized
tags:
  - iPhone
  - Mail
  - 인증서
---
[iPhone supports alternate CA root certs!][1]을 보면 iPhone에 내가 만든 루트 인증서를 추가할 수 있다. 간단히 pem 파일을 사파리로 열고 거기서 등록하면 된다.

이렇게하면서 iPhone으로 회사메일을 확인하고, 보낼 수 있게 되었다.

추가로 회사에서는 아웃룩으로 pop3를 사용하는데, 여기서는 아이폰에서는 imap을 사용한다. 즉.. 아이폰을 이용해서 보낸 메일은 imap상의 보낸 편지함에 보관되겠지만, 회사 아웃룩에는 보관이 안되는 상태가 된다.

이 문제는 아이폰에서 보낼때 아래처럼 "자신을 항상 숨은 참조로"를 설정해놓고, 아웃룩에서 pop3로 받을 때 규칙으로 보낸 편지함으로 옮기면 되겠네요.

[<img class="aligncenter size-full wp-image-408" title="iphone_bcc" src="http://blog.woosum.net/wp-content/uploads/2010/09/iphone_bcc.png" alt="" width="320" height="480" />][2]

 [1]: http://nsayer.blogspot.com/2009/01/iphone-supports-alternate-ca-root-keys.html
 [2]: http://blog.woosum.net/wp-content/uploads/2010/09/iphone_bcc.png