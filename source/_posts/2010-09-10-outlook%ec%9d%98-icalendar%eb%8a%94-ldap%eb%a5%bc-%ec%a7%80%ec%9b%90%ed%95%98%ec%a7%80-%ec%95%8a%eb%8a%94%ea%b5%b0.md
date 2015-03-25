---
id: 428
title: Outlook의 iCalendar는 ldap를 지원하지 않는군
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=428
permalink: /archives/428
dsq_thread_id:
  - 875001574
tags:
  - iCalendar
  - Outlook
---
iCalendar에는 <Directory Entry Reference>를 지원합니다. 아래와 같은 형식이죠.

    ORGANIZER;DIR="ldap://host.com:6666/o=eDABC%20Industries,c=3DUS??
      (cn=3DBJim%20Dolittle)":jimdo@host1.com

이렇게 iCalendar를 만들고 Outlook에서 가져오면, 역시나 아웃룩에서 안가져오고 무시해버립니다. 역시나 2% 부족한 MS에요.