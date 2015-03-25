---
id: 1544
title: Ironic 테스트 1차 정리
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1544
permalink: /archives/1544
dsq_thread_id:
  - 2776784911
tags:
  - ironic
  - OpenStack
---
Ironic이 아직 인큐베이팅이라서 그런지 아직 안되는게 많네요.

 - 이미지를 ami 형태로 가야하네요. 기존 qcow라면 이미지를 이중으로 관리해야하는..
 - 노드의 spec을 일일이 지정해야 한다는... 노드마다 spec이 다르면 지옥이겠네요
 - 당연할지 모르지만 ironic은 snapshot을 지원하지 않습니다. (SoftLayer baremetal은 되는데)
 - deploy 이미지와 부팅 이미지는 별도로 가야하는... 이중 부팅이라 어쩔 수 없죠
 - 근데 tftp로 전송되는 이미지는 생각보다 커서.. 문제가 될 듯
 - pxe에 사용되는 tftp는 직접 설정해야 하지만, tftpd-hpa와는 호환 안됨
 - 막상 설정을 마치고 배포해야 하는데 cli가 없어서 API를 직접 호출.. ㅎㅎ
 - undeploy.. 즉. 회수하고 다른 이미지로 설치하는 방법이 없음... 블루프린트도 아직 안 나왔군요
 - nova와 연계가 아직... Ironic에서 만든 노드는 nova에서 안보이고, nova에서 ironic으로 지정해서 부팅하려면 버그 투성이고 ㅠㅠ
 - neutron 과의 연계가 잘 안되네요.. 그래서 pxe 부팅을 시작하기 위해 neutron에 삽질을...
 - 특정 tenant의 네트워크에 ironic 노드를 만들 수 없어요.. 이걸 하려면 switch 설정하는 것도 ironic에 필요할텐데... 전혀  없죠^^ 여기에. sdn이 필요할까요?
 - 가상화 하는 곳과 별도의 cell로 구성이 필요
 - 물론 문서대로 하면 잘 안되고, 소스 보면서 해야 됩니다. ㅎㅎ
 - 그리고 당연히 버그도 많아요 ㅠㅠ

근데 아마 가장 첫번째로 닥치는 문제는 개발환경 만들기군요 ㅎㅎ

정신 건강을 위해서 아직 시도하지 않는게 좋을 듯 합니다.