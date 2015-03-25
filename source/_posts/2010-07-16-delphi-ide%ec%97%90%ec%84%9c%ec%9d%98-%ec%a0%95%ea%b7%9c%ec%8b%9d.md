---
id: 292
title: 'Delphi IDE에서의 정규식...'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=292
permalink: /archives/292
dsq_thread_id:
  - 723795309
tags:
  - Delphi
  - IDE
---
    hi=:param

을

    hi = :param

으로 변경하고 싶습니다. 정규식을 써야겠지요? 그래서 Replace에서 정규식을 적었더니 이상하게 잘 못 찾습니다. 도움말을 봤죠. 그랬더니 약간 제가 알고 있던 정규식과는 이상한 부분이 있데요.

> { } Braces group characters or expressions. Groups can be nested, with a maximum number of 10 groups in a single pattern. For the Replace operation, groups are referred to by a backslash and a number, according to the position in the "Text to find" expression, beginning with 0. For example, given the text to find and replacement strings, Find: {[0-9]}{[a-c]*}, Replace: NUM\1, the string 3abcabc is changed to NUMabcabc.

그냥 괄호"()"를 쓰는게 일반적인 정규식으로 알고 있었는데, 여기선 중괄호"{}"를 사용하네요.

> Text to find: {[a-zA-Z]}=\:{[a-zA-Z]}
> Replace with : \0 = :\1

{% img /images/delphi-ide-replace-regex-300x238.png %}

ps 1. 그리고 IDE 검색기능에 전체 프로젝트 파일에서 찾는것까진 정말 좋습니다. 그런데 여기서 추가로 찾은 것에 대해서 바꾸기나 프로젝트 전체에서 바꾸기 그런거 있으면 좋겠네요. 또한 dfm 파일에서도 찾을 수 있으면 좋겠네요.

물론 다른 에디터를 실행해서 할 순 있지만, 귀찮을 때가 있어요.
