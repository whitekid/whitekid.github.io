---
id: 917
title: 'request_module: runaway loop modprobe binfmt-464c'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=917
permalink: /archives/917
dsq_thread_id:
  - 755156741
---
가상 머신을 띄우는데... 아래처럼 당황스런 메시지가 뜨면서, 커널이 올라오지 않는다.

    request_module: runaway loop modprobe binfmt-464c

인터넷을 뒤져보니 해당 문제는 32비트 커널을 사용하는 머신에서 64비트 바이너리를 실행할 때 생기는 문제라고 한다. 즉 다시 말해서 내가 뭔가 잘못 건드려서 커널이 32비트로 바뀐거다. 전혀 한 기억이 없는데 말이다.

  * https://bbs.archlinux.org/viewtopic.php?pid=253794

역시 경험으로 느낀건... 가상머신의 커널은 그냥 가상머신 안에 두는 것이 좋겠다. 요즘 리눅스는 hvm으로 돌아간다고  하드라도, 기본적으로 pv driver가 설치되어있기 때문에 io가 문제가 없으니 말이다.