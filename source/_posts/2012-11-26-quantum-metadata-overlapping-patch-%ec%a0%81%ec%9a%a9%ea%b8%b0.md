---
id: 1135
title: 'quantum: metadata overlapping patch 적용기'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1135
permalink: /archives/1135
dsq_thread_id:
  - 944687139
categories:
  - Uncategorized
tags:
  - OpenStack
  - Quantum
---
quantum의 문제중에 하나인 [overlapping_ip을 사용할 때 metadata service가 작동하지 않는 문제][1]에 대해서 이전에 언급했었고, [이에 대한 패치][2]가 저번주에 올라왔습니다.

오늘 이를 테스트 해봤는데 대략 잘 동작합니다.

간단한 구조는 다음과 같습니다.

{% img https://docs.google.com/drawings/pub?id=1T4Iq8p2tjnfcqvmfeCxwDVfdlYx7CAOAXJub4TRTe1k&w=822&h=614" %}

  1. metadata request는 <http://169.254.169.254/latest/metadata> 형태로 router namespace로 들어온다.
  2. router namespace의 NAT rule에 의해서 localhost의 port 9697로 redirect 된다.
  3. 9696에서 listen하는 process(quantum-ns-metadata-proxy)는 l3_agent에서 network 생성에 맞춰 관리되며, namespace당 하나씩 생성된다.
  4. quantum-ns-metadata-proxy는 파라미터로 router_id를 가지므로 자신이 담당하는 network을 알 수 있다.
  5. quantum-ns-metadata-proxy는 nova-metadata-agent가 필요한 정보를 모아서(instance-id, router-id, network-id) http header에 포함하여 quantum-metadata-agent로 proxy request를 보낸다.
  6. quantum-metadata-agent는 ns-metadata-proxy에서 넘어온 전보를 바탕으로 instance-id를 확인하고 이를 http header에 포함하여 nova-metadata-api에 보낸다.
  7. nova-metadata-api는 X-Instace-ID가 있으면 이 아이디를 기준으로 메타데이터를 넘긴다.

대략 순서를 정리하면 이렇습니다. 마지막으로 quantum-metadata-agent를 upstart service로 등록하여 작업하는 것 까지하면 대략 쓸만할 것 같습니다.

지금은 우선 테스트라 quantum-metadata-agent가 같은 서버로 들어갔지만, metadata_ip를 줄 수 있으므로 별도의 서버에 운영할 수 있겠습니다.

## 삽질기

위 패치는 nova/ quantum 2개로 구성이 되어있으며, 당연히도 master 소스를 기준으로 작성이 되어있습니다. 그런데 저는 테스트 환경을 ubuntu folsom cloud archive를 사용하기 때문에 folsom 용으로 만들어야 했지요.. git에서 작업하기 위해 몇 가지 삽질을..

  1. master에 patch 적용하고 patch
  2. stable/folsom 기준으로 branch를 하나 만들고...
  3. master의 commit를 cherry picking...
  4. 다시 folsom에 맞게 소스 수정하고 commit...
  5. folsom과 변경된 부분으로 patch 생성...

물론 nova/ quantum도 같이요...

 * upstart-job service 등록

## Side effect?

이전에 metadata 접속 방식은 l3 router namespace에서 metadata api로 요청하는 것이었습니다. 이 경우 l3 router namespace 부터는 public ip address이므로 결국 metadata api server의 주소도 public ip address를 가지게 됩니다.

그런데 이 metadata api server는 굳이 외부에서 접속할 필요가 없으므로 public ip를 가질 필요가 없습니다. 따라서 뭔가 꺼림직 했었는데, 이번 패치를 적용하면, quantum-metadata-agent를 내부망에서 운영할 수 있으므로 metadata api server가 내부망에서 운영이 가능합니다.

 [1]: https://blueprints.launchpad.net/quantum/+spec/metadata-overlapping-networks
 [2]: https://review.openstack.org/#/q/topic:bp/metadata-overlapping-networks,n,z
 [3]: https://docs.google.com/drawings/pub?id=1T4Iq8p2tjnfcqvmfeCxwDVfdlYx7CAOAXJub4TRTe1k&w=822&h=614