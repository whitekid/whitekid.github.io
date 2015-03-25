---
id: 343
title: postfix outgoing mail moderation
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=343
permalink: /archives/343
dsq_thread_id:
  - 726571614
categories:
  - Uncategorized
tags:
  - email
  - postfix
  - postfix-moderation
---
이메일 서버를 postfix를 사용하고 있습니다. 이런 중에 외부로 나가는 메일을 허락받고 보내는 솔루션을 검토할 필요가 생겼습니다. MS Outtlook에서는 email modeeration이라는 기능으로 이무 구현이 되어있더군요. 그런데 postfix용으로 이런게 있는지 뒤져보는데, 역시나 없습니다. 물론 하고자하는 일이 영 껄그런기능이라 그런지 그런 질문에 부정적인 답볃도 많구요. CCTV를 설치하고 감시하는게 어떠냐는 등등.. ^^;

어찌되었던간 postfix로 간단하게 구현했습니다.

smtpd\_data\_restriction에서 check\_policy\_service를 이용해서 처리하고, 검토되어야하는 메일일 경우에는 메시지를 HOLD하고 moderator에게 확인 메시지를 보내고 그 결과에 따라서 처리합니다.

**main.cf:**  
[code lang="plain"]smtpd\_data\_restrictions = check\_policy\_service unix:private/moderation[/code]  
smtp\_data\_restriction을 이용한건 다른 곳에서는 해당 메시지의 queue id가 넘어오지 않아서 입니다.

**master.cf:**  
[code lang="plain"]moderation         unix  -      n       n       -       0       spawn  
user=nobody argv=/usr/local/bin/python /home/whitekid/work/postfix-moderation/postfix-moderation.py[/code]  
이렇게 주면 smtp에서 데이터가 넘어올때 master.cf에 설정된 moderation 서버스에 의해서 메시지가 처리됩니다.

**posfix-moderation.py**

postfix-moderation.py는 smtpd policy 서비스를 하는 것으로 자세한 프로토콜은 [Postfix 웹사이트][1]에 있습니다. sender, recipient를 읽어서 외부로 나가는지 확인하고 정상적이면 action=dunno를 아니면 action=hold를 리턴하면 됩니다. 소스는 아주 간단합니다. 약 주석을 포함해서도 100줄 정도 되는군요

여기서 hold된 상태들은 PostgreSQL에 관리된 DB에 들어갑니다(간단한 것이라 SQLite등 보다 가벼운 것으로 할 수 있지만, PostgreSQL이 설치되 사용중이라 그걸로 했습니다.).

## cron.py:

이렇게 DB에 메시지들이 설정되면 cron.py에서 메일 보내가나 큐를 조작하는 일들을 합니다. 1분 간격으로 실행되면서 DB를 검토해서 새로운 검토가 필요한 메시지면 moderator에 메시지를 보내고, moderator가 allow 또는 deny한 것들을 처리힙니다.

큐에서 postcat -bh [queue_file]을 이용해서 큐의 메시지를 이메일 메시지로 얻어오고 이 정보로 확인하죠.  
[code lang="python"]email.message\_from\_file(os.popen('/usr/local/sbin/postcat -bh /var/spool/postfix/hold/%s' % queue_id))[/code]  
또 문제되었던게 원본 메시지를 포함하여 moderator에게 메일을 보내는 건데..

[code lang="python"]  
def attach_message(msg, attach):  
base = MIMEBase('message', 'rfc822&')  
base.attach(attach)  
msg.attach(base)

msg = MIMEMultipart()  
msg['From'] = 'moderation@' + MY_DOMAIN  
msg['To'] = moderator  
msg['Subject'] = u'[메일 전송 확인] %s' % subject  
msg.attach(MIMEText(message.encode('utf-8&'), \_subtype='html', \_charset='utf-8&'))

attach\_message(msg, orig\_message)  
server = smtplib.SMTP()  
server.connect('localhost')  
server.sendmail('moderation@' + MY\_DOMAIN, moderator, msg.as\_string())  
server.close()  
[/code]

이런식으로 보냅니다.

메일 큐 조작은 postsuper를 이용하면 됩니다. -H 옵션은 홀드된 메시지를 다시 보내는 것이고, -d 옵션은 큐를 지웁니다. 따라서 allow된 것은 release하고 postqueue -i [queue_id]로 바로 보내도록 설정합니다.

## cgi/moderate.py

cron에서 moderator에게 메일을 보내면 여기에는 allow/deny 링크가 포함됩니다. moderator는 이 링크를 이용해서 메일을 허락 또는 거부하죠.

물론 여기에서는 DB를 조작하는 것 빼고는 아무것도 없습니다. 당연히 모든 작업은 cron.py에서 하니깐요.  
[code lang="plain"]Alias /moderate "/home/whitekid/work/postfix-moderation/cgi/moderate.cgi"

<Directory "/home/whitekid/work/postfix-moderation/cgi">  
AddHandler cgi-script .cgi  
Options NONE +ExecCGI  
Order allow,deny  
Allow from all  
</Directory>[/code]  
정신없이 대충 적어서 무슨 말인지 도저히 저도 모르겠군요. ^^; 소스요? 그건 좀 소스정리좀 하구요...

 [1]: http://www.postfix.org/SMTPD_POLICY_README.html