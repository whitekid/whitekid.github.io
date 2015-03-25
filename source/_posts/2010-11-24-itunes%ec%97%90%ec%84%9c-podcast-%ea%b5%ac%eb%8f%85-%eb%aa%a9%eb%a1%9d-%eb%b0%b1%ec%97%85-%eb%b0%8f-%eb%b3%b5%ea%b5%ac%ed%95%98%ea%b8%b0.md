---
id: 619
title: iTunes에서 Podcast 구독 목록 백업 및 복구하기
author: whitekid
layout: post
guid: http://www.woosum.net/?p=619
permalink: /archives/619
dsq_thread_id:
  - 714747165
tags:
  - iTunes
  - podcast
---
iTunes의 podcast 구독목록 백업은 생각보다 간단하다. 하지만 문제는 자세히 보지 않으면 찾기가 힘들다는 것이 문제다. DVD Writer가 있다면 그냥 모두 백업하고 복구하면 간단한 이야기 일 수 있지만, 그렇게까지 하고 싶지는 않다. 그냥 구독 목록만 다른데서 복구되고, 업데이트된 것만 받으면 되니깐..

구독목록 백업을 하려면 왼쪽 패널에서 Podcast를 선택하고 컨택스트 메뉴에서 내보내기를 선택한다.

여기까지는 뭐 다 아는 이야기일 것고, 나도 [구글링][1]을 통해서 [찾아보기][2] 전까지는 이렇게 했었다. 메뉴를 선택하고 그냥 백업해보면 좀 당황스럽다. 그냥 Podcast 목록이 백업된다. 내가 원한건 이게 아니었는데, 구독 목록이었는데... 그렇치 여기까지 나도 그랬었다.

자 다시 백업파일 입력하는 대화상자에서 파일 타입을 OPML로 선택하고 저장해보자. 그리고 저장한 파일을 텍스트 편집기로 열어 확인해보자. 내가 원한건 이거다!

{% img /images/podcast_subscription_backup.png %}

그런데 다시 이것을 복구하는 방법은?.. 열심히 메뉴나 기타를 뒤져보지만 역시 나오지 않는다. Apple이 자주 사용하는 기능들은 편하지만, 좀 특별하거나 복잡한 기능들은 꼭꼭 숨겨놓는다.

그냥 그 백업 파일을 iTunes로 드래그하면 podcast가 아주 간단하게 추가된다. 아주 허무할 정도로...

PS. iTunes U는 적용이 되지 않는다. 백업은 되는데 백업된 파일을 추가할 수가 없다. 이건 수동으로 해야하는 모양이다. ㅡㅡ;

PS1. 스마트 재생목록도 목록이 아니라 설정들이 저장되면 좋겠는데, 그게 안된다. ㅡㅡ

PS2. 스마트재생목록은 Podcast 처럼 내보낼때 XML 형식으로 하면 되는 것 같다. 가져오기는 파일 -> 보관함 -> 재생목록 가져오기를 하면 된다.

 [1]: http://www.google.co.kr/search?q=itunes+popcast+subscription+backup&ie=utf-8&oe=utf-8&aq=t&client=firefox-a&rlz=1R1GGLL_ko___KR390#hl=ko&newwindow=1&client=firefox-a&hs=erP&rlz=1R1GGLL_ko___KR390&&sa=X&ei=U27sTI-xNciycJLvsfEO&ved=0CCsQvwUoAQ&q=itunes+podcast+subscription+backup&spell=1&fp=902d01aef5c2abb7
 [2]: http://www.mahalo.com/answers/what-is-the-best-way-to-backup-my-itunes-podcast-subscription-list#a3ec677833f9760d12d165685d19eaf65ac950622e2bb9eb1a8744182b3c752da