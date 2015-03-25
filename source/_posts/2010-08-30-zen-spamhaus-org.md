---
id: 372
title: zen.spamhaus.org
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=372
permalink: /archives/372
dsq_thread_id:
  - 736070928
tags:
  - postfix
  - rbl
---
이전까지 스팸 호스트 막으려고 spamlist.or.kr와 sbl.spamhaus.org를 두가지 사용하고 있었다. 그런데 점점 스팸이 늘어나고, 그 패턴을 보니 내용으로 검사하기엔 힘들다. 헤더에 특별한 내용도 없고, 제목도 항상 바뀌고, 딸랑 링크 한줄에 그 링크 주소도 계속 바뀌고, 링크 클릭하면 한곳으로 redirect되고... 역시나 대부분 러시아 산이다.

무식하게 access, body_check에 넣으려다 다시 spamhaus의 rbl을 또 공부해봤는데... sbl뿐문 아니고 다른 것도 많다. 또 그때 했었던 서버스 말고 다른 서비스도 많이 늘었다. 그래서 바꿨더니 아직 하루도 되지 않았지만 상당한 성과가 있네..

하는 김에 더 많이 넣어버렸다.

    smtpd_client_restrictions = foo bar...
            reject_rbl_client spamlist.or.kr
            reject_rbl_client zen.spamhaus.org
            reject_rbl_client cbl.abuseat.org
            reject_rbl_client dnsbl.sorbs.net

5시간 정도 적용한 결과다..

    $ grep spamhaus /var/log/maillog | wc -l
          28
    $ grep spamlist /var/log/maillog | wc -l
          78
     grep abuseat /var/log/maillog | wc -l
           2
    $ grep sorbs /var/log/maillog | wc -l
           3

(하루 지나니 보니 192, 99, 2, 7로 변했음) 이게 많은지 모르면.. 이 전날 통계를 보면...

    $ bzgrep -E -o  'blocked using ([a-zA-Z\.]+)' /var/log/maillog.1.bz2 | sort | uniq -c
     65 blocked using spamlist.or.kr

대략 괜찮은 듯..