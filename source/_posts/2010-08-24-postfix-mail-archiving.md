---
id: 354
title: postfix mail archiving
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=354
permalink: /archives/354
dsq_thread_id:
  - 750514319
categories:
  - Uncategorized
tags:
  - email
  - postfix
  - postfix-archiver
---
[메일을 보낼때 검토][1]받아서 보내는 부분은 보류되었습니다. 역시나 하나가 끝나면 하나가 다시 오는법... 검토는 하지않지만 주고받은 메일을 archiving하는 것으로 선회했습니다. moderation은 영 정말로 껄그럽지만, archiving은 그보다는 좀 낫네요.

가장 간단한 방법은 모든 <a href="http://www.postfix.org/postconf.5.html#always_bcc" target="_blank">always_bcc</a>으로 모든 메일 메시지를 특정 계정으로 보내면 됩니다. archiving용 계정 하나 만들고 모두 보내면 되니깐 편하겠지요.

그런데 모든 메일입니다. archiving에 필요없는 메시지도 가는 것이지요. 뭐 메일 root@로 날아오는 관리일이나 각종 보안 검사메일, cron 명령 메일등등 이런것을 굿이 archiving할 필요가 없는데 말입니다. 그래서 가장 간단하지만 always\_bcc를 사용하지 않기로하고, 그런 다음에 눈에 들어온게 content\_filter입니다.

content_filter를 만들어서 거기서 메일을 저장하면 되겠지요. 간단한 내용이지만 이거 메커니즘 이해하느라 상당히 고생했습니다. <a href="http://www.postfix.org/FILTER_README.html" target="_blank">postfix 샘플</a>대로 하니 안됩니다. 계속 loop를 돌아서 에러를... ㅡㅜ;

## 설정

archiver 필터를 archiver라고 하면

main.cf:

[code lang="plain"]content_filter = archiver[/code]

이제 모든 메시지는 archiver 서비스를 통해서 전달됩니다.

master.cf:

[code lang="plain"]  
archiver unix - n n - 10 pipe  
flags=Rq user=whitekid null_sender=  
argv=/usr/local/bin/python /home/whitekid/work/postfix-archiver/archiver.py 127.0.0.1 20026 ${sender} ${recipient}

127.0.0.1:20026 inet n - - - - smtpd  
-o content_filter=  
-o receive\_override\_options=no\_unknown\_recipient\_checks,no\_header\_body\_checks,no_milters  
-o smtpd\_helo\_restrictions=  
-o smtpd\_client\_restrictions=  
-o smtpd\_sender\_restrictions=  
-o smtpd\_recipient\_restrictions=permit_mynetworks,reject  
-o mynetworks=127.0.0.0/8  
-o smtpd\_authorized\_xforward_hosts=127.0.0.0/8[/code]

  * archiver 서비스에서 필터를 실행합니다.
  * 그리고 파라미터들은 필터를 처리하고 난 다음의 action들을 지정하는데 archiver 필터를 처리한 후 127.0.0.1:20026 서비스를 호출하기 위해 파라미터를 전달해줍니다. 이 파라미터는 archiver.py에서 받아서 다음 서비스에 전달합니다. 아래 서비스가 smtpd이므로 smtp로 메일을 보내는 것 처럼 합니다.

content_filter -(pipe)-> archiver -(smtp)-> 127.0.0.1:20026 의 순서로 메시지가 전달됩니다.

## archiver.py

이제 archiver.py의 대략적인 소스를 보면...

[code lang="python"]  
import sys  
import smtplib  
import email

EX_OK = 0  
EX_TEMPFAIL = 75  
EX_UNAVAILABLE = 69

def main():  
host, port, sender, recipient = sys.argv[1], int(sys.argv[2]), sys.argv[3], sys.argv[4:]  
msg = email.message\_from\_string(sys.stdin.read())  
msg['X-Archive-Status'] = 'Archived'

server = smtplib.SMTP(host, port)  
for r in recipient:  
server.sendmail(sender, r, msg.as_string())  
server.quit()

if \_\_name\_\_ == '\_\_main\_\_':  
try:  
main()  
sys.exit(EX_OK)  
except Exception, e:  
print e  
sys.exit(EX_TEMPFAIL)  
[/code]

내용은 아주 간단합니다. stdin에서 받은 내용에서 약간의 메시지 조작을 하고, 그 메시지를 다시 파라미터로 받은 smtp로 연결해서 넘김니다. 자 이렇게고 maillog를 보면

[code lang="plain"]Aug 24 14:23:39 freebsd postfix/qmgr[22880]: 537F51705A: from=<<root@freebsd.woosum.net>>, size=1013, nrcpt=1 (queue active)  
Aug 24 14:23:39 freebsd postfix/pipe[22890]: 3D3E217030: to=<<whitekid@freebsd.woosum.net>>, relay=archiver, delay=0.1, delays=0.01/0.01/0/0.09, dsn=2.0.0, status=sent (delivered via archiver service)  
Aug 24 14:23:39 freebsd postfix/qmgr[22880]: 3D3E217030: removed  
Aug 24 14:23:39 freebsd postfix/local[22894]: 537F51705A: to=<<whitekid@freebsd.woosum.net>>, relay=local, delay=0.01, delays=0.01/0/0/0, dsn=2.0.0, status=sent (delivered to mailbox)  
Aug 24 14:23:39 freebsd postfix/qmgr[22880]: 537F51705A: removed[/code]

음 archiver 서비스를 이용해서 mailbox가지 간게 보이나요? 보이면 OK!

## amavisd-new와 공존

amavisd도 content\_filter를 사용합니다. 그런데 이게 2개가 적용되지 않습니다. content\_filter는 1개인것이고, 이게 chain으로 역여서 돌아가는 것이지요. 이렇게 chain에 넣어야하는데, 그건 간단하게...

[code lang="plain"]amavis unix - - - - 2 smtp  
-o smtp\_data\_done_timeout=1200  
-o smtp\_send\_xforward_command=yes

127.0.0.1:10025 inet n - - - - smtpd  
-o content_filter=archiver[/code]

처럼 content_filter에 archiver를 넣어주면 됩니다.

 [1]: /2010/08/postfix-outgoing-mail-moderation/