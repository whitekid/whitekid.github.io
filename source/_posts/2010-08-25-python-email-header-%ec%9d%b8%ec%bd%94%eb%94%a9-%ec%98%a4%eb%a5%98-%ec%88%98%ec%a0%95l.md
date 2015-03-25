---
id: 367
title: python email header 인코딩 오류 수정l
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=367
permalink: /archives/367
dsq_thread_id:
  - 714426926
categories:
  - Uncategorized
tags:
  - email
  - python
---
[postfix mail archiving][1]을 진행하면서 메시지 헤더를 처리할 일이 생겼지요. 보내는 사람에 따라서 archiving을 하지 않으려구오. 뭐... cron에서 보내는거 daily 체크 메일등등 저장할 필요 없잖아요.

그런데 이거 하다보니 헤더 디코딩이 안된 것들이 간간히 들어옵니다. 언듯 봐서는 잘된것 처럼 보이는데 그래서 좀 찾아보니 python의 오류가 아니고, 아예 잘못된 헤더랍니다.

[code lang="plain"]  
=?UTF-8?B?6rmA7ZiV7ISd?=<<XXXX@naver.com>>'  
=?UTF-8?B?6rmA7ZiV7ISd?= <<XXXX@naver.com>>'  
[/code]

첫번째 것은 잘못 인코딩 된 것이고, 두번째 것은 제대로 인코딩 된 것입니다.[ RFC에 따르면 "?=" 다음에 스페이스를 넣게 되어있다는군요][2].

그래서 이거 해결하는 코드 간략하게 만들어 봤습니다. 아주 간단!!

[code lang="python"]  
def decode_header(s):  
ret = []  
for header, encoding in email.header.decode_header(s):  
if (encoding is None) and re.search(r'\?=.+', header):  
header = re.sub(r'([^\?.]{2})(\?=)(.{1})', r'\1\2 \3&', header)  
ret.extend(email.header.decode_header(header))  
continue  
ret.append((header, encoding))  
return ret  
[/code]

근데.. 이런 오류는 꼭 NAVER에서 온 메일만 그래요... ㅡㅡ

ps. re.sub에서 [a-zA-Z0-9]는 \w로 바꿀 수 있다구요? 예 유니코드 환경이 아니면 맞는 이야기인데, 유니코드면 약간 다르더군요. Python 메뉴얼에는 이렇게 나와있습니다.

> \w  
> When the LOCALE and UNICODE flags are not specified, matches any alphanumeric character and the underscore; this is equivalent to the set [a-zA-Z0-9\_]. With LOCALE, it will match the set [0-9\_] plus whatever characters are defined as alphanumeric for the current locale. If UNICODE is set, this will match the characters [0-9_] plus whatever is classified as alphanumeric in the Unicode character properties database.

 [1]: /2010/08/postfix-mail-archiving/
 [2]: http://bugs.python.org/issue8132