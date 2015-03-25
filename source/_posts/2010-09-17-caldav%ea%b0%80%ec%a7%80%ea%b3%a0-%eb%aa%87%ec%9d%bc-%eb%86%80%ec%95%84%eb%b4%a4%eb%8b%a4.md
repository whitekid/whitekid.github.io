---
id: 442
title: CalDav가지고 몇일 놀아봤다.
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=442
permalink: /archives/442
dsq_thread_id:
  - 714677990
categories:
  - Uncategorized
tags:
  - CalDav
  - Calendar
  - iCal4GL
  - Lightning
  - Outlook
---
## 문제의 시작: iPhone <-> Google Calendar <-> Outlook

iPhone에 캘린터더를 보면 구글 캘린더와 연동이 된다. 여기서부터 출발이다. 그래 이거 좋은데! 그럼 회사에서 사용하는 Outlook과도 연동은 어떻게 할까? 하고 찾아봤다. 당연히 구글에서 [Outlook과 연동하는 소프트웨어를 제공][1]한다. 자 이렇게해서 iPhone <-> Google Calendar <-> Outlook과의 calendar의 연동이 가능해졌다.

### Tasks는?

Google tasks도 연동이 되면 좋겠지만, 안된다. 돈주면 된다고 한다.

## 회사에서 사용할 수 있을까?

이거 괜찮습니다. 여기저기 캘린더가 연동되어서 일정관리가 되고, 서로 연동이 되니 정말 괜찮군요. 그럼 회사내에서 사용할 수 있을까요? 물론 Exchange 서버를 사용한다면 문제가 안되겠지요!! 하지만 Exchange를 사용하지 않고있기 때문에 문제가 생깁니다. 그럼 어떻게 할 것인가..

### CalDav

여기 저기 찾다보니, 캘린더에대한 인터넷 표준이 있네요. 역시 사람은 공부해야합니다.

CalDav 서버를 설치하고 Outlook으로 지원하면 되겠다고 생각했습니다. 오픈소스 CalDav서버인 [davical][2]을 검토해보니, Apache + PostgreSQL의 조합이네요. 아주 좋습니다. 설치는 아주 간단하고 쉽게 되었고, 이제 Outlook의 차례..

근데 안됩니다. ㅡㅜ 좀더 알아보니 Outlook은 CalDav를 지원하지 않을 거라고합니다. 그런데 Exchange는 CalDav를 지원한다고 합니다. 생각해보면 MS로서는 일리있는 선택이지요. Outlook이 Exchange와 연동되어서 사용되어야지 다른 CalDav를 지원하는 서버와 연동이 된다면 Exchange 서버를 죽이는 결과가 나올 수 있으니깐요. 그래서 Outlook는 CalDav를 지원하지 않습니다. 이러니 테스트조차 할 수 없고, 그래서 다른 것을 알아보니

Thunderbird에 캘린더 부가기능인 [Lightning][3]은 CalDav를 지원합니다. 굿,~ 설치해 테스트하니 정말로 잘됩니다. 실시간으로 전송하니, 약간의 딜레이도 있고 몇가지 버그는 보이지만 잘 됩니다. 그렇다고 Outlook를 모두 Thunderbird로 바꿀 수 없고ㅡㅜ;,

### Outlook CalDav 지원 Plugin: iCal4GL

그러다가 찾은 것이[ iCal4GL][4]입니다. CalDav 뿐만 아니라 여러 공개 캘린더 프로토콜을 지원하는 캘린터 Sync 플러그인이죠. 설정이 조금 복잡하지만 테스트 하니 그럭저럭 됩니다. 단 가장 중요한 하나만 빼놓구요.

바로 한글 지원이 안됩니다. 아마 유니코드 문제인가하고 인코딩 설정이 있어서 바꿔 줘 봤지만 역시나 안됩니다. 이거 참 ....

## 결론

  1. 개인적으로 사용하려면 Google calendar sync를 사용하면 된다.
  2. CalDav라는 캘린더 표준이 있고 davical이라는 오픈소스 서버도 있다. 아지민 Outlook은 지원하지 않고, 지원하지 않을 것이다.
  3. Outlook에서 캘린더 공유는 Exchange 밖에 없다. 아니면 Outlook Plugin을 만들어야 한다.
  4. 그것도 아니라면 Google calendar를 사자, 그렇게 되면 이메일 및 기타 서비스도 같이 구글로 간다. ^^;
  5. Lignting도 좋은 선택이다. 단 사람들의 반발만 없으면...

## Lightning의 버그

여러 캘린터를 사용할 경우 Check 박스를 클릭하다보면 캘린터 일정이 안보입니다. ㅡㅜ;

 [1]: http://www.google.com/support/calendar/bin/answer.py?hl=en&answer=98563
 [2]: http://davical.org/
 [3]: http://www.mozilla.org/projects/calendar/lightning/
 [4]: http://ical.gutentag.ch/