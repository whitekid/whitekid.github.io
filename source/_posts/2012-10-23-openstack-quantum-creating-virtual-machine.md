---
id: 990
title: 'Openstack + Quantum: Creating Virtual Machine'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=990
permalink: /archives/990
dsq_thread_id:
  - 896063452
tags:
  - OpenStack
---
간단하게 작성해본 OpenStack with Quantum 환경에서 tenant-private network과 external network을 만들고 해당 네트워크 상에 가상머신을 생성하는 테스트 스크립트입니다.

테스트용이므로 실전에서는 사용할 수 없을 정도이고, 그냥 참고만 하세요

    #!/bin/bash
    IMAGE=${IMAGE:-cirros-0.3.0-x86_64}

    if [ -z "$OS_TENANT_NAME" ];
      echo "openstack environ variables is not set"
      echo "please run . ~/openrc tenant_name"
      exit
    fi

    TENANT_ID=$(keystone tenant-list | grep " $OS_TENANT_NAME " | awk '{print $2}')
    PRINET="${OS_TENANT_NAME}"
    EXTNET="ext_net"

    VM=$1

    #
    # create external network - only by admin
    #
    if [ "$OS_USERNAME" = 'admin' ]; then
      EXTNET_ID=$(quantum net-list -- --tenant_id=$TENANT_ID --router:external=True | awk "/ $EXTNET / { print \$2 }")
      if [ -z "$EXTNET_ID" ]; then
        EXTNET_ID=$(quantum net-create $EXTNET --tenant_id=$TENANT_ID --router:external=True | grep ' id ' | awk '{print $4}')
      fi

      # create external subnet
      EXTSUBNET_ID=$(quantum net-show $EXTNET_ID | awk "/ subnets / { print \$4 }")
      if [ $EXTSUBNET_ID = "|" ]; then
        EXTSUBNET_ID=$(quantum subnet-create $EXTNET_ID "10.100.1.0/24" --tenant_id=$TENANT_ID --name=${EXTNET}_subnet --enable_dhcp=False | awk '/ id / {print $4}')
      fi
    else
      EXTNET_ID=$(quantum net-list -- --router:external=True | awk "/ $EXTNET / { print \$2 }")
    fi

    #
    # tenant internal network
    #

    # create private network
    NET_ID=$(quantum net-list -- --tenant_id=$TENANT_ID --name=$PRINET | awk "/ $PRINET / { print \$2 }")
    if [ -z "$NET_ID" ]; then
      NET_ID=$(quantum net-create $PRINET --tenant_id=$TENANT_ID | grep ' id ' | awk '{print $4}')
    fi
    echo "NET=$NET_ID"

    # create private subnet
    SUBNET_ID=$(quantum net-show $NET_ID | awk "/ subnets / { print \$4 }")
    if [ $SUBNET_ID = "|" ]; then
      SUBNET_ID=$(quantum subnet-create $NET_ID "172.16.1.0/24" \
            --tenant_id=$TENANT_ID --name=${PRINET}_subnet \
            --dns_nameservers list=true 168.126.63.1 8.8.8.8 | \
            awk '/ id / {print $4}')
    fi
    echo "SUBNET=$SUBNET_ID"

    # now internal network is working
    # and connect to external network

    # create router for connect to external network
    ROUTER_NAME="router_${OS_USERNAME}_ext"
    ROUTER_ID=$(quantum router-list -- --tenant_id=$TENANT_ID --name=$ROUTER_NAME | head -n -1 | tail -n +4 | awk '{print $2}')
    if [ -z "$ROUTER_ID" ]; then
      ROUTER_ID=$(quantum router-create --tenant_id=$TENANT_ID $ROUTER_NAME | awk '/ id /{print $4}')
    fi
    echo "ROUTER=$ROUTER_ID"

    quantum router-interface-add $ROUTER_ID $SUBNET_ID
    quantum router-gateway-set $ROUTER_ID $EXTNET_ID

    #
    # boot instance
    #
    if [ ! -z "$VM" ]; then
      IMAGE_ID=$(nova image-list | grep " $IMAGE " | head -n 1 | awk '{print $2}')
      echo "IMAGE=$IMAGE_ID"

      VM_ID=$(nova boot --image=$IMAGE_ID --flavor=1 --nic net-id=$NET_ID $VM | awk '/ id /{print $4}')
      echo "VM=$VM_ID"

      nova show $VM_ID
    fi

이 스크립트는 https://github.com/whitekid/openstack-chef/ 의 일부입니다.~~