---
layout: post
title: "keystone bootstrap"
date: 2015-03-25 15:46:41 +0900
comments: true
categories: 
tags:
    - openstack
    - keystone
---
keystone bootstrap 이란 명령이 있는 것을 오늘 처음 알았다.. ^^

\1. 프로젝트를 만들고 2. 유저를 만들고 3. 해당 유저를 프로젝트에 어떤 롤로 지정한다..

이것을 한번에 수행한다. 이전에 이거 하려고 몇번 명령 내렸던 뻘짓을...

    $ keystone  help bootstrap
    usage: keystone bootstrap [--user-name <user-name>] --pass <password>
                              [--role-name <role-name>]
                              [--tenant-name <tenant-name>]
    
    Grants a new role to a new user on a new tenant, after creating each.
    
    Arguments:
      --user-name <user-name>
                            The name of the user to be created (default="admin").
      --pass <password>     The password for the new user.
      --role-name <role-name>
                            The name of the role to be created and granted to the
                            user (default="admin").
      --tenant-name <tenant-name>
                            The name of the tenant to be created
                            (default="admin").
