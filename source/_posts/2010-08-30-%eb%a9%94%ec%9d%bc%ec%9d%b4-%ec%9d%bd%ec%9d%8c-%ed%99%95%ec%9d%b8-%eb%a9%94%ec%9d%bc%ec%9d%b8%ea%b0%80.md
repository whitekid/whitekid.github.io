---
id: 381
title: 메일이 읽음 확인 메일인가?
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=381
permalink: /archives/381
dsq_thread_id:
  - 716544555
---
메일 메시지가 읽은 확인 메시지(Message Disposition Notification: MDN)인지 확인하려면

  * RFC[3798][1], RFC[2298][2]에 따르면 top-level의  content type이 Content-Type: multipart/report;
    report-type=disposition-notification;
    인 것이다. : RoundCube, JavaMail(SNU)...
  * 역시나 MS는 나름데로 방법이다. RFC는 안지키는 것같고 Content-Type: application/ms-tnef;
    name="winmail.dat" 형식의 mime에 첨부파일이 있다.

ps. 정말로 오랜만에.. 근 10년 되었나? RFC보니 정말로 느낌이 새롭네.. 거참...

 [1]: http://www.faqs.org/rfcs/rfc3798.html
 [2]: http://www.faqs.org/rfcs/rfc2298.html