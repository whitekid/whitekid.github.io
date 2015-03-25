---
id: 1432
title: 'haproxy: https redirect, ip block 등등..'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1432
permalink: /archives/1432
dsq_thread_id:
  - 2026130261
categories:
  - Uncategorized
tags:
  - haproxy
---
haproxy는 loadbalancing 기능 이외에도 다양한 기능이 많습니다.

/admin을 내부 아이피만 허용하는 경우는 아래처럼 할 수 있습니다.

[code]  
acl is-internal-net src 172.16.0.0/12 192.168.0.0/16 10.0.0.0/8  
acl is-admin url_beg /admin  
block if !is-internal-net is-admin  
[/code]

http를 https로 redirect하는 것도 haproxy에서 사용할 수 있죠.

[code]  
redirect scheme https if !{ ssl_fc }  
[/code]

haproxy가 ssl offload 기능을 제공하기에 backend에는 http만 사용합니다. 그리고 밑의 application에서 https에서 온 request라는 것을 확인하기 위해서 x-forwarded-protocol을 붙여줍니다.

[code]  
bind 0.0.0.0:443 ssl crt <your-certificate-file>  
acl is-ssl dst_port 443  
reqadd x-forwarded-protocol:\ https if is-ssl  
[/code]

종합하면 아래처럼 되겠습니다.

[code]  
listen webapp  
bind 0.0.0.0:443 ssl crt <your-certificate-file>  
server server1 10.10.10.10:80 check  
server server1 10.10.10.11:80 check  
acl is-internal-net src 172.16.0.0/12 192.168.0.0/16 10.0.0.0/8  
acl is-admin url_beg /admin  
acl is-ssl dst_port 443  
block if !is-internal-net is-admin  
redirect scheme https if !{ ssl_fc }  
reqadd x-forwarded-protocol:\ https if is-ssl  
[/code]