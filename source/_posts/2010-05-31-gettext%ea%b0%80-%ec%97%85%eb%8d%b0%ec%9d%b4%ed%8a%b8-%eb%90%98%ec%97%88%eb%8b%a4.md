---
id: 117
title: gettext가 업데이트 되었다.
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=117
permalink: /archives/117
dsq_thread_id:
  - 762743890
tags:
  - FreeBSD
---
gettext가 0.18로 업데이트 되었다.

이놈은 항상 무섭다. 엄청나게 많은 ports들이 의존성을 갇고 있기 때문에(portmaster -r gettext로 보면, 직간접적으로 X11이 돌아가는 시스템은 147개, X11이 안돌아가는 놈은 90개) 함부로 업데이트 했다가는 날리가 난다. 그런데 쩝.. 업데이트 된 줄도 모르고 다른거 업데이트 하다가 의존성 때문에 업데이트가 되고 말았다. 이런 실수 실수...

어쩔수 없이 gettext가 업데이트 된 상황을 반영해야한다.

  portmaster -w -r gettext

하다가 기존의 라이브러리 참조하는에러가 나서 임시로 라이브러리를 기존 것으로 symbolic 걸어놓고 다시 gettext 업데이트 하고 있다.

조심하자...

추가

  1. portmaster 2.32버전으로 올라가면서 -w옵션이 추가되었는데, 공유 라이브러리는 백업을 하고 업그레이드를 하기 때문에 위처럼 symbolic link를 거는 삽질을 안해도 된다.
  2. portmaster 걸때 시간도 같이 측정했으면 재밌었을 텐데... 그냥 하고 있으므로 무시...