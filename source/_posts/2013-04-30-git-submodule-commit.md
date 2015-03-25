---
id: 1248
title: git submodule commit
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1248
permalink: /archives/1248
dsq_thread_id:
  - 1248001537
tags:
  - bash
  - git
---
git으로 작업하면서 야심차게 submodule을 도입해봤습니다. 그런데 서브모듈에서 커밋하는 순간부터 막히기 시작하네요.

원칙으로 한다면 submodule을 커밋 & push하고 parent도 commit & push 해 줘야하겠지요. 근데 submodule을 커밋하려면 해당 모듈이 있는 디렉토리로 이동해서 해야합니다.

    $ cd submodule && git commit -m 'this is submodule commit message' -a

하나라면 그런데로 할만한데.. 이게 2개 이상이면 좀 짜증이 나기 시작합니다. 그래서 간단하게 쉘 스크립트를 만들어 해봤지요.


녭.. 이런거 해주는 비슷한 것이 있습니다. git foreach 명령이죠.

    $ git submodule foreach git commit -m 'this is submodule commit message' -a

그런데 커밋하면 바로 에러가 납니다. 이유는 서브 모듈 중에서 변경되지 않은, 커밋될 필요가 없는 것은 위의 git commit 명령이 오류를 내면서 정지하기 때문이죠. 그래서 모두 커밋하려면 위에서 처럼 분기가 필요한데.. 이를 foreach와 묶는 것을 한 줄로 처리하기 힘들죠..

그리고 된다고 하더리도 아마도 -m 메시지의 공백때문에 제대로 처리가 되지 않을 겁니다.

Update: [gist][1]가 좋아보여서 거기다 스크립트 넣어봤습니다. 괜찮네요.

Update: push하는 것도 적용해봤습니다.

 [1]: https://gist.github.com