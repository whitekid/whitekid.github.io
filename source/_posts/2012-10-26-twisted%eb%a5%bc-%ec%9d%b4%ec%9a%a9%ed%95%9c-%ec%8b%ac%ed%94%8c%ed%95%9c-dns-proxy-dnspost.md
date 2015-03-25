---
id: 1040
title: 'twisted를 이용한 심플한 dns proxy: dnspost'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1040
permalink: /archives/1040
dsq_thread_id:
  - 900799264
categories:
  - Uncategorized
tags:
  - dns
  - dnspost
  - MadeByKidd
  - proxy
---
Mac OSX의 [resolver][1]라는 기능이 있습니다. 간단히 말해서 특정 도메인에 해당하는 DNS 쿼리를 특정 DNS 서버에 요청하는 것이지요.. 뭐 일상생활에서는 필요없겠는데, 개발용으로 내부 네임서버를 돌리고 있는 경우에 가끔 유용하게 쓸 수 있습니다.

아래처럼 도메일별로 다른 서버에 DNS 쿼리가 가능하다는 것이죠.

  * *.dev.company.com -> 192.168.1.199
  * *.myteam.company.com -> 192.168.1.200
  * 나머지 -> 168.126.63.1

OSX에서는 resover를 사용해서 하고.. 또한 우리 회사 개발자도 OSX를 좋아해서 이 기능을 쓰라고 그러고, 다른 OS는 별로 관심이 없습니다.(OSX가 기본 개발 OS인양...). Ubuntu를 데스크탑으로 사용하는 제가 이상하게 소외받는 ㅎㅎ

그래서 목마른 사람이 우물을 판다고 python twisted name 라이브러리를 이용해서 간단히 뚝딱뚝딱 만들어 봤습니다. 로컬에서 네임서버 돌리고 이 것을 사용하면 됩니다.

<https://github.com/whitekid/dnspost>

### Installation

    $ apt-get install python-twisted-names  
    $ git clone <https://github.com/whitekid/dnspost.git>  
    $ cd dnspost  
    # edit dnspost.conf listen_port  
    $ ./dnspost.py  

### Ubuntu/NetworkManager note

ubuntu desktop에서는 NetworkManager를 사용하고, NetworkManager에서는 dnsmasq를 사용하여 dns를 캐슁하면서 로컬에서 서비스 하고 있습니다. NetworkManager에서 dnsmasq가 상위 dns 쿼리할때 특정 포트로 쿼리할 수 있는 기능이 있다면 로컬에서 별도의 포트로 돌리고있는 dnspost로 설정하여 할 수 있지만, 그런 기능이 없기에 dnsmasq를 사용하지 않고 합니다.

#### disable dnsmasq

/etc/NetworkManager/NetworkManager.conf에서 dns=dnsmasq라는 라인을 삭제합니다

    #dns=dnsmasq  

NetworkManager restart

    $ service network-manager restart  

px ax | grep dnsmasq로 dnsmasq가 없는지 확인합니다.

#### set nameserver to localhost

네트워크 연결 설정에서 DNS 서버를 127.0.0.1로 변경합니다. 설정을 변경한 후 /etc/resolv.conf에는 설정이 없거나 127.0.0.1로 설정이 됩니다.

만일 반영이 안되면 network-manager를 다시 시작합니다.

    $ service network-manager restart  

#### register dnspost upstart service

    $ cp dnspost.conf /etc/init/dnspost.conf  
    $ ln -s /lib/upstart-job /etc/init.d/dnspost  
    $ service dnspost start  

#### test

    $ dig daum.net localhost  

잘 동작하는 것을 확인하였다면 이제 설정파일 적당히 바꿔서 도메인별로 DNS Server를 다르게 사용해보시기 바랍니다. - 끝 -

 [1]: http://developer.apple.com/library/mac/#documentation/Darwin/Reference/ManPages/man5/resolver.5.html