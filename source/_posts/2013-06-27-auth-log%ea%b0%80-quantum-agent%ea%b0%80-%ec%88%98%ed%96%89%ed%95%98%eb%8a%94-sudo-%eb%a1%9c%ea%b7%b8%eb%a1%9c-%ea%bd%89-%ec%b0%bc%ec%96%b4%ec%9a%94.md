---
id: 1337
title: auth.log가 quantum agent가 수행하는 sudo 로그로 꽉 찼어요..
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1337
permalink: /archives/1337
dsq_thread_id:
  - 1440395432
tags:
  - auth.log
  - Quantum
  - sudo
---
어제 시스템 팀에서 연락이 왔습니다. /var/log/auth.log에 아래처럼 지속적으로 quantum-agent가 남기는 로그가 남는다구요.

    Jun 23 06:33:26 network sudo: pam_unix(sudo:session): session opened for user root by (uid=106)
    Jun 23 06:33:26 network sudo: pam_unix(sudo:session): session closed for user root
    Jun 23 06:33:26 network sudo: pam_unix(sudo:session): session closed for user root
    Jun 23 06:33:26 network sudo: quantum : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/usr/bin/quantum-rootwrap /etc/quantum/    rootwrap.conf ovs-vsctl -timeout=2 get Interface tapae222d3c-61 external_ids
    Jun 23 06:33:26 network sudo: pam_unix(sudo:session): session opened for user root by (uid=106)
    Jun 23 06:33:26 network sudo: quantum : TTY=unknown ; PWD=/ ; USER=root ; COMMAND=/usr/bin/quantum-rootwrap /etc/quantum/    rootwrap.conf ovs-vsctl -timeout=2 get Interface tapae222d3c-61 external_ids

음.. 그렇군 하면서 보니까 /var/log/auth.log에는 로그가 정신없이 쌓이고 있군요. 한 호스트에 일주일에 쌓인 로그가 약 500M 정도입니다. 한 호스트에 이정도이니 다른 quantum-agent가 동작중인 network, compute 노드를 합치면.. 헐... 이 녀석을 처리하기로 했습니다. 당연히 모든 sudo의 로그를 날릴 수 없고, quantum agent가 날리는 sudo 명령만 없에기로 했습니다.

/var/log/auth.log는 어디서 생기느냐.. 당연히 아시겠지만 syslog가가 auth,authpriv.*인 내용만 골라서 분리한 내용입니다. Ubuntu의 경우는 rsyslog를 사용하기 때문에 /etc/rsyslog.d/50-default.conf에 정의되어 있지요.

    auth,authpriv.*                 /var/log/auth.log

즉... 어디선가 syslog로 날리는 저 내용을 없애면 되는 것이죠.

로그 내용을 보면

  1. sudo에서 이용하는 pam의 세션이 열리고 닫혔다.
  2. sudo로 수행하는 명령은 어떤 것이다.

입니다. 첫번째 로그와 두번째 로그를 날리는 주체는 다릅니다. 첫번째는 pam.d, 두번째는 sudo...

두번째는 간단합니다. /etc/sudoers.d/quantum_sudoers에서 아래 라인을 추가하면 됩니다.

    Defaults        logfile=/var/log/sudo.log
    Defaults        !syslog

내용 보면 간단하죠? syslog로 로그를 남기지 않고 /var/log/sudo.log로 남깁니다. 그래서 /var/log/authl.log에 로그가 가지 않습니다.

여기까지 하고 /var/log/auth.log를 보면 여전히 세션이 열리고 닫힌 기록이 계속 남습니다. 이는 pam.d에서 해 줘야하는 것이죠. 그래서 /etc/pam.d/sudo를 보면

    @include common-auth
    @include common-account
    @include common-session-noninteractive

이렇게 나와있습니다. 세션에 관련딘 내용은 세번째 줄 같죠? 맞습니다. 조금 긴 내용이 나옵니다. 여기서 잠깐 위의 로그 파일을 자세히 보면 ...

    Jun 23 06:33:26 lion034 sudo: pam_unix(sudo:session): session closed for user root

뭔가 보이나요? 그렇죠.. pam\_unix라는 곳에서 로그를 남기고 있다고 친절하게 알려주고 있습니다. 다시 pam\_unix 관련된 라인이 있는지 찾아보면

session required        pam_unix.so

라고 친절히 있습니다. 그렇습니다.. 여기서 뭔가 해줘야 하겠는데.. 어떤게 있을까 하고 뒤져보니깐... 방법이 아주 간단히  있더군요.

    session [success=1 default=ignore] pam_succeed_if.so service in sudo quiet use_uid
    session required        pam_unix.so

자세한 내용은 모르겠고, sudo service에서 성공한 인증은 로그를 무시하라는 이야기 이군요.

자... 여기까지 하면... 그렇게 많이 쌓이던 로그가 갑자기 없어지는 신기한 경험을 합니다~~ ㅎ~

그리고 마지막으로 quantum sudo log를 /var/log/sudo.log로 보낸 것 기억하시죠? 이것까지 마무리 하면 되겠습니다.

/etc/logrotate.d/sudo:

    /var/log/sudo.log {
      daily
      missingok
      compress
      delaycompress
      notifempty
    }

추가) 이런 방법 말고 rsyslog에서 필터링 하는 방법도 있군요. /etc/rsyslog.d/50-default.conf의 시작 부분에 다음을 추가합니다.
    :msg, contains, "uid=106"
    & ~

    :msg, contains, "ovs-vsctl"
    & ~

참고:

  * http://ubuntuforums.org/showthread.php?t=1256801
  * http://www.ruwebit.net/article/406