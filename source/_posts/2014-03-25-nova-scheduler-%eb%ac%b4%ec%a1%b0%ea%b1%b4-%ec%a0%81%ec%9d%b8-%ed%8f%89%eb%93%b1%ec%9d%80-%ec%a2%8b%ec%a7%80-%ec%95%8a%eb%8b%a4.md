---
id: 1477
title: 'nova-scheduler: 무조건 적인 평등은 좋지 않다.'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1477
permalink: /archives/1477
dsq_thread_id:
  - 2503833611
categories:
  - Uncategorized
tags:
  - nova-scheduler
  - OpenStack
---
어느 순간 알림이 옵니다. m1.large 인스턴스를 #개 만들 수 있는 여유밖에 없습니다. 지속적으로 인스턴스를 만들다 보니 컴퓨팅 리소스가 부족해진 것이지요.. 우선 급하게 nova-compute용 장비 집어넣고 가만히 상황을 살펴 보자니, 상황이 조금 이상합니다.

m1.large의 인스턴스를 만들 메모리가 있는 하드웨어는 없지만, 전체 여유 메모리를 보면 충분히 m1.large를 수용할 만큼 남아있습니다.

아래와 같은 상황을 봅시다.

{% img https://docs.google.com/drawings/d/1dh0fy7raVF-zcFTQO9vXikOCdtgLvh-tkirE93KI-zU/pub?w=498&h=210 %}

이 상황에서 메모리가 3G인 인스턴스 만든다면 Host D에 인스턴스를 만들 수 있습니다. 하지만 1G 인스턴스를 먼저 만들면 Host D에 인스턴스가 생성되어 아래처럼 됩니다.

{% img https://docs.google.com/drawings/d/1JUMp1Hb4IImNjupH4c4LaENjPQfQP3eJRIwnVvMecrg/pub?w=498&h=210 %}

다시 메모리 3G 인스턴스를 만든다면 3G 메모리가 여유있는 컴퓨팅 노드가 없기 때문에 인스턴스를 만들 수 없습니다. 하지만 메모리의 총량은 1+2+1+2=6G로 충분히 3G짜리 인스턴스를 2개 만들 수 있는데도 말입니다.

이렇게 되는 이유는 nova-scheduler에서 인스턴스가 만들어질 호스트를 선택하는 scheduling을 이해하면 간단한 산수로 조정할 수 있습니다.

## filter scheduler & weigher

[nova developer documentation][1]의 아래 그림을 보면 쉽게 이해가 가능합니다.

{% img http://docs.openstack.org/developer/nova/_images/filteringWorkflow1.png %}

nova-scheduler는 컴퓨트 노드 중에서 생성하려는 인스턴스를 위치시킬 호스트를 선택합니다. 대략 과정을 보면..

첫 번째로 nova.conf의 scheduler\_default\_filters 설정된 필터로 위치시키면 안되는 호스트를 제외합니다. RamFilter는 메모리가 부족한 것을 제외.. ComputeFilter는 vCPU가 부족한 호스트를 제외합니다.

두 번째로, 선택된 호스트 중에서 scheduler\_weight\_classes에 지정된 가중치를 준 후, 그 중에서 가장 높은 점수를 가진 호스트를 선택합니다.  기본값은 nova.scheduler.weights.all_weigher로 모든 weigher를 적용합니다. 하지만 weigher는 RAMWeigher만 존재하고, 즉 메모리만 가중치를 주게 되어있습니다. 즉 최종적으로는 아래처럼 가중치를 계산합니다.

> weight = 1.0 x host.free_ram_mb

즉.. 인스턴스는 가장 메모리가 여유가 많은 호스트에 위치합니다. 이게 평균적으로 합리적인 것 같지만, 처음 이슈 제기처럼 총 메모리는 여유 있어도, 메모리가 많이 필요한 인스턴스를 만들 수 없습니다. ^^;

## weigher를 조절

그럼 위 문제를 해결하기 위해서 1G의 작은 인스턴스는 메모리가 많이 할당된 것으로 보내는 방법으로 가면 어떨까요? 간단히 weighting을 메모리 역순으로 한다면?

이 것도 문제가 발생하죠. 한 호스트에 인스턴스가 만들어지면, 해당 호스트는 메모리가 가장 작을 것이므로, 다음 인스턴스도 그 호스트에 만들어집니다. 결국 한 호스트의 메모리가 다 차면, 다음 호스트에 만드는 현상이 발생합니다. 문서에서는 spread와 stack으로 표현하고 있습니다.

결국 이 것도 해결 방법이 아닙니다. 따라서 아래의 결론에 다다릅니다.

  * 메모리가 작게 필요한 인스턴스는 메모리가 작게 남은 호스트에 위치한다.
  * 메모리가 크게 필요한 인스턴스는 전과 같게 메모리가 큰 호스트에 위치한다.

즉, 아래 그림처럼 1G 인스턴스를 Host A에 만들어서, Host B 또는 Host D에 만들어져, 3G 인스턴스를 만들 여유 메모리를 소모하지 않고, 다음에 Host B, D를 만들 수 있게 합니다.

{% img https://docs.google.com/drawings/d/1r3CwVZ4IQKzWH-90ZmJpG5xeI4fbGlVtpKBz6ID8ZAI/pub?w=498&h=210 %}

## RAMStackWeigher

이 것을 weigher로 만들면 아래처럼 몇 줄 안되는 weigher가 됩니다. 코드는 머 대충 돌아갑니다. ^^;

    class RAMStackWeigher(weights.BaseHostWeigher):  
      def _weight_multiplier(self):  
        return 1.0
    
    def _weigh_object(self, host_state, weight_properties):  
      instance_type = weight_properties['instance_type']  
      instance_memory_mb = instance_type['memory_mb']  
      free_ram_mb = host_state.free_ram_mb
      
      if instance_memory_mb > CONF.ram_weight_stack_min:  
        return free_ram_mb  
      else:  
        if free_ram_mb <= CONF.ram_weight_stack_host_free_ram:  
          return - free_ram_mb  
        else:  
          return free_ram_mb - CONF.ram_weight_stack_host_free_ram - (1024 * 256)  
    [/code]

참고) weigher는 모든 호스트에 대해서 \_weigh\_object를 호출하고 \_weight\_multiplier 순으로 호출됩니다.

scheduler\_weight\_classes는 ListOpt 이므로 다른 weight를 주고 싶다면, 다른 weigher를 만들어서 옵션에 등록하면 됩니다.

## 결론...

기본 nova-scheduler는 평등을 지향합니다. 평등은 좋은 것 처럼 보이지만, 자원을 효율적으로 사용하지는 못하는 문제가 있습니다. 효율화를 위해서는 약간(?)의 차별은 필요합니다.

 [1]: http://docs.openstack.org/developer/nova/devref/filter_scheduler.html