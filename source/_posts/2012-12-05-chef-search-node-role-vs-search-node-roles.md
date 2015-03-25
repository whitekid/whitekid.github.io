---
id: 1180
title: 'chef: search node role vs search node roles'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1180
permalink: /archives/1180
dsq_thread_id:
  - 958961887
categories:
  - Uncategorized
tags:
  - chef
---
knife를 이용해 특정 role을 가지는 role을 검색하려면 아래처럼 합니다.

$ knife search node role:openstack-compute

그런데 다른 소스를 뒤져보다 아래와 같이 queury하는 것을 봤습니다.

$ knife search node roles:openstack-compute

뭘까? 하고 그냥 지나치다가 찾아봤죠.. [Find Nodes with a Role in the Expanded Run List][1]라고 부르는군요.

간단히 예를 들면

  * nodeA: role-A
  * nodeB: role-B
  * role-A: recipe[A], role[base]
  * role-B: recipe[B], role[base]

이라고 되어있을 경우

$ knife search node role:base

로 할 경우 role에 직접적으로 base가 지정되어 있지 않기 때문에, 검색결과가 없습니다.

$ knife search nodes role:base

로 할 경우는 role-A를 확장하여 role-A, recipe[A], role[base]를 대상으로 찾기 때문에 nodeA, nodeB가 모두 검색이 됩니다.

또한 하나의 side-effect로 role이 expand되는 때는 실제로 chef-client가 실행하면서죠.. 따라서 chef-client가 정상적으로 실행되고 나서야 roles로 검색이 가능합니다. 즉 roles로 검색된 결과는 해당 role로 할당된 노드가 있고, chef-client 정상적으로 cookbook을 실행했다는 의미죠.

 [1]: http://wiki.opscode.com/display/chef/Search#Search-FindNodeswithaRoleintheRunList