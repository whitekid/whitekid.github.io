---
id: 460
title: apache on jailed environment
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=460
permalink: /archives/460
dsq_thread_id:
  - 858961504
tags:
  - ezjail
  - FreeBSD
  - jail
---
유지하고 있는 서비스가 있습니다. 근데 이놈이 문제가 생겼습니다. 포트의 특정한 버전을 요구합니다. 그러나 해당 그 포트는 계속 업그레이드 하고있지만, 그놈의 서비스는 업그레이드가 안되고 있습니다. 결국 현재 서비스되고 있는 현제 그상태로 계속 유지되어야 합니다.

그런데 다른 서비스들은 그렇지 않습니다. 다른 서비스는 계속 업데이트 되고... 골치아픕니다.

생각해보다가 그 문제가 되는 서비스를 jail 안으로 옮겨버리기로 했습니다. ^^; 녜!! 관리 안되는 놈 감옥에 가둘겁니다.

## jail?

자세한 것은 [Handbook][1]을 참조하시기 바라며, 간단하게 chkroot의 향상된 버전이고 kernel 레벨에서 구현되는 process를 시스템과 완전히 분리하는 가상화 시스템 비슷한 것이다.

또한 커널 레벨이기 때문에 베이스 시스템과 커널을 공유한다.

## ezjail 설치

native로 제공된는 jail을 쓰려다가 sysutils/ezjail이 더욱더 좋겠습니다. 이것을 쓰기로 합니다.

    $ portmaster sysutils/ezjail

ezjail은 basejail을 만들어야 합니다. 그래서 다음 명령으로 basejail을 만듭니다.

    $ ezjail-admin update -b

  * -b: buildworld를 수행합니다. buildworld가 이미 되었다면 대신 -i 옵션을 줍니다.
  * -p: 각각의 jail에 portstree를 제공합니다. 이것 또한 모든 jail에 공유합니다.

-b 옵션을 주었다면 buildworld를 하기 때문에 꽤 걸립니다. ^^;

## apache용 jail 만들기

jail 환경을 만드는 건 쉽습니다. 간단히 아래 명령으로

    $ ezjail-admin create apache_service 192.168.0.10

  * apache_service: 호스트 이름
  * 192.168.0.10: 만들어질 호스트에 할당할 ip address

대부분의 파일들을 basejail과 공유하기 때문에 금방 만들어 집니다. 아래 명령으로 해당 jail의 console로 들어가서 열심히  작업을 해봅니다.

    $ ezjail-admin console apache_service

물론 많은 작업이 안될겁니다. 기본적으로 인터넷 연결이 안되어서 거의 아무것도 할 것이 없습니다. 음.. 아파치 컴파일 해야하는데 인터넷이 연결안되니 할게 없군요.

## 내부 인터페이스 설정

jail 환경하에서는 보안상 네트웍 카드 설정을 할 수 없습니다. 호스트 환경으로 나와서 아래처럼 내부 내트웍을 구성해줍니다.

    [root@host /]# ifconfig lo1 create
    [root@host /]# ifconfig lo1 inet 192.168.0.10 netmask 255.255.255.0 alias

이 작업을 부팅시에도 하려면 /etc/rc.con에 다음과 같이 추가해줍니다.

    cloned_interfaces="lo1"
    ifconfig_lo1="inet 192.168.0.10 netmask 255.255.255.0"

그리고 jail에서 internet에 접근할 수 있도록 NAT 를 pf를 통해서 설정해줍니다.

**/etc/pf.conf**

    nat on re0 from lo1:network to any -> (re0)

이제 다시 ezjail console로 들어가서 인터넷이 되는지 확인해봅시다.

    apache_service# nslookup daum.net 8.8.8.8

(8.8.8.8은 [google에서 제공하는 dns 서버][2]인데, 주소가 기억하기 쉬어서 씁니다.) 별 문제 없으면 daum.net의 아피 주소들이 주루륵 나올겁니다. 녜.. 이제 jail에서 외부로 연결이 완료되었군요.

## apache 띄우기

이제 jail 환경에서 www/apache22를 설치하고 서비스를 실행합니다(뭐 여기는 다 알거니깐 자세한건 생략)

그런 다음 호스트에서 jail 환경의 apache를 접근해 보도록 합니다.

    [root@host /]# lynx http://192.168.0.10/

친근하게 It works! 라고 뜨지요. 예 host에서는 jail로 잘 연결됩니다. 그럼 외부에서 이 jail 환경의 apache로 접근이 될까요? 물론 안됩니다. 녜 jail 환경은 분리된 네트웍이기 때문이지요.

이걸 해결하는 방법은 2가지가 있습니다. 첫번째는 jail에 NAT 환경을 구성했으니 port forwarding을 설정하면 됩니다. 하지만 이 경우에 같은 포트에 여러 다른 jail을 구성하려면 안되지요. 이런 경우를 위해서 두번째 apache의 mod_proxy를 이용합니다.

## port forwarding to jailed apache

port forwarding은 간단합니다. /etc/pf.conf에 다음처럼 추가하면 끝

    rdr on re0 proto tcp from any to ${public_ip_address} port http -> 192.168.0.10 port http

## mod_proxy

mod\_proxy는 www/apache22에서 PROXY, PRXY\_HTTP 옵션을 켜고 다시 컴파일하고, 각 모듈을 로드합니다.

**httpd.conf**

    LoadModule proxy_module         libexec/apache22/mod_proxy.so
    LoadModule proxy_http_module    libexec/apache22/mod_proxy_http.so

그리고 jailed apache로 서비스할 것을 설정해 줍니다.

    <VirtualHost *:80>
      ServerName jail.woosum.net

      ProxyPass / http://192.168.0.10/
    </VirtualHost>

이제 아파치를 재시작하고 http://jail.woosum.net/phpinfo.php 로 접속해보면 HTTP_HOST가 192.168.0.10으로 나오는 것을 확인할 수 있다.

## 결론

  * jails를 이용하면 각각의 서비스를 분리할 수 있다.
  * sysutils/ezjail을 이용하면 jails를 편하게 관리할 수 있다.
  * 원칙적으로 각 jail 마다 하나의 public ip를 가지지만 NAT, port forwarding, mod_proxy 등을 이용하면 내부 내트웍 만으로도 구성할 수 있다.

참고:

  * http://www.woosum.net/wiki/Ezjail

ps. 대부분의 jail 관련 예들은 public ip address를 가진 jail을 기준으로 되어있었습니다. 그래서 한참을 해멨네요.

 [1]: http://www.freebsd.org/doc/handbook/jails.html
 [2]: http://code.google.com/speed/public-dns/index.html