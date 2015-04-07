---
layout: post
title: "kubernetes review"
date: 2015-04-06 21:34:06 +0900
comments: true
categories:
tags:
  - kubernetes
---
지난주에 구글과 미팅을 하다가 아무래도 우리가 OpenStack 및 클라우드에 중심을 두고 이야기하다 보니 당연히 [Kubernetes](http://kubernetes.io) 이야기가 나왔다.

그들의 이야기로는 알려진 것 처럼 구글에서는 워크로드의 99%가 컨테이너 위에서 동작하고, 그리고 그 것을 오픈소스화한 kubernetes가 1.0이 나오면 엄청난 발전을 할 것이라고 하도 자랑을 해서 다시 한번 봐보기로 했다.

물론 그 전에 봤을때 아직은 쓸만한 것이 아니라고(그때는 홈페이지도 볼만한 수준이 아니였다.. 뭐.. 그건 지금도 마찬가지) 판단했지만, 그 이후에 mirantis가 google과 kubernetes를 오픈스택에 올리는 것을 협의한다는 뉴스(murano 이야기인데, 그게 murano의 기반을 kubernetes로 간다고 생각했는데, 지금보면 murano app에 kubernetes를 포함하는 것 아닐까 한다.)까지 나와서 다시 한번 봤다.

## 콤포넌트 구성

### etcd
`CoreOS`에서 나온 Key/Value Store로 `kubernetes`는 모든 데이터를 여기가 저장한다. 나중에 나오는 `flannel`도 여기다 데이터를 저장한다.

[etcd는 여러 많은 곳에서 production에 사용하고 있다](https://github.com/coreos/etcd/blob/master/Documentation/production-ready.md)고 하니깐.. 알아서 쓰시면 되겠다.

덧) [Consul](http://consul.io)를 사용하는게 더 나을 것 같다.

### master

master는 사용자 API Request를 받아들이고, Application replica를 유지하고, kube-apiserver, kube-controller-manager, kube-scheduler로 구성된다.

 * ***kube-apiserver*** 말 그대로 apiserver
 * ***kube-controller-manager*** `etcd`를 모니터하고 있다가 `replica` 설정이 변경되면 API를 이용해서 replication을 유지한다.
 * ***kube-scheduler*** 실행될 minion 찾기

### minion

minion은 그냥 worker다. minion을 사전에서 찾아보면 앞잡이, 부하라는 뜻이다.

 * ***docker*** 당연히 application은 docker에서 실행된다.
 * ***kubelet*** node agent, docker를 실행하고 `kube-proxy`를 통신을 위한 iptables 설정하는 등의 역할 수행
 * ***kube-proxy*** pod <-> pod, pod <-> service간의 traffic을 처리한다. 네트워킹에서 아주 중요한 요소

## Application 구성

Application은 Container의 그룹인 pod와 이를 관리하는 replicationController, 이를 외부와 통신하는 Service로 구성된다. 자세한 내용은 [Design 문서](https://github.com/GoogleCloudPlatform/kubernetes/blob/master/DESIGN.md#kubernetes-api-server)를 참고한다.

### ReplicationController & POD

ReplicationController는 확장하는 단위다. 간단히 말해서 AWS AutoScalingGroup의 단위가 된다. [guestbook-go](https://github.com/GoogleCloudPlatform/kubernetes/tree/master/examples/guestbook-go).

그리고 이 한 단위는 container의 묶음이 된다. 이 묶음 하나를 POD라고 한다. 즉 ReplicationController는 POD로 갯수를 관리한다.

nginx / uwsgi + flak / api / cache/ db로 구성된 App을 생각해보면 아래처럼 Scale Group을 나누게 될 것이고, 이게 ReplicationController가 된다.

  * nginx + uwsgi + flask
  * api
  * cache
  * database

### Service

이렇게 ReplicationCluster가 여러 POD를 구성하는데, 각 POD는 독립적인 IP를 가진다. 근데 이를 외부 또는 내부에서 접근하기 위해서는 `ElasticIP`(또는 vip)와 같은 방식으로 접근하는데 이게 Service이다.

## Network Model

Kubernetes의 네트워크 모델은 GCE(Google Compuging Engine)에 종속적인 느낌이다. Network provider가 연결된 NIC에 subnet을 routing 해주는 기능이 필요하다(이 기능을 GCE는 하고 있다).

GCE가 아니면 VALN, [flannel](https://github.com/coreos/flannel) 또는 다른 것(Weave, OpenVSwitch...)을 이용해서 POD간에 internal network을 overlay network으로 구성하면 된다.

하지만 이것도 문제가 있다.

### VLAN

kubenetes pod에서 사용할 큰 VLAN/ Subnet을 잡아놓고, 각 Node마다 subnet을 작게 할당해주면 가능하다.

하지만, 이렇게 할 경우 사람 손으로 한다는 것은 힘들고, 자동화를 시켜야한다. 자동화를 한다는 것에 중요한 것은 이 프로젝트를 본격적으로 해보겠다는 이야기인데, 익숙해지기 그리고 완전히 파악하기 전까지는 힘든 일이겠다.

### Software Overlay

1. 기본적으로 노드(minion)에 subnet(ex /24)를 할당하게 되어있어서, 네트워크 자원을 효율적으로 쓰지 못한다. /24를 할당했다면 255개의 POD를 수용가능하지만, 이건 컴퓨팅 파워, Application의 성격에 달린 문제이기 때문에 완전히 효율적을 사용하지 못한다.

2. POD Network은 overlay network이므로 flannel이 설치된 곳에서만 사용이 가능하다. 결국 외부로 통신하기 위해서는 한쪽에서 LB 또는 NAT를 이용하여 gateway 기능을 해줘야한다. 결국 여기서 traffic 집중 현상이 발생하다.


## 결론

1. ReplicationController로 부터 시작하는 개념은 좋다. 이렇게 가면서 자연스럽게 클라우드/클러스터 중심으로 생각을 시작할 수 있을 것 같다.

1. multi tenent/ 인증 모델이 없다. endpoint만 알면 아무나 사용할 수 있는 구조이다.

1. kube-proxy에 traffic이 몰린다. 향후 계획에는 부하를 줄이기 위해서 user space proxy인 kube-proxy 대신 iptables NAT를 이용한다고 하지만, 여전히 software 기반이기 때무에 한계는 있을 것이다.

1. multi-master가 아니다. 뭐 이건 중요한 문제이기 때문에 곧 해결될 것으로 보인다.

1. etcd가 신뢰 할 만한지는 아직 모르겠다..

1. 역시 가장 큰 문제는 GCE에 종속된 네트워크 구조이다. 이거 때문에 GCE가 아닌 환경에서는 상당히 고생하지 않을까한다.

## 덧...

이거 하면서 OpenStack에 어떻게 접목시킬까 고민하고 있었는데... 뭔가 실마리를 찾앗다. 이거 수정하려면 go를 공부해야하는데.. ... 음... golang은 왠지 안땡겨서...
