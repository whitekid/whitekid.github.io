---
id: 1320
title: 'chef: definition과 notifies'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1320
permalink: /archives/1320
dsq_thread_id:
  - 1411764469
tags:
  - chef
---
[Chef Definition][1]은 아주 간단하게 Resource를 만들 수 있습니다. 아래 코드를 보면 간단하게 ~/.ssh/authorized_keys를 설정하는 Resource를 만들 수 있지요

    define :authorized_keys_for, :keys => nil, :group => nil, :home => nil do
      user = params[:name]
      group = params[:group] || user
      home = params[:home] || "/home/#{user}"
      keys = params[:keys].kind_of?(String) ? [params[:keys]] : params[:keys]

      if not keys.nil? and not keys.empty?
        directory "#{home}/.ssh" do
          owner user
          group group
          mode 0700
          action :create
          only_if "test -d #{home}"
        end

        file "#{home}/.ssh/authorized_keys" do
           owner user
           group group
           content keys.map{ |x| x.strip }.join("\n")
        end
      end
    end

뭐.. 간단한 일이면 이런 거 사용하면 됩니다. 저도 별 무리 없이 사용하고 있다가.. 여기서 notifies를 걸여줘야할 것이 생겼습니다. authorized_keys를 설정한 이후에 뭔가를 하려는 것이지요. 그래서 뭐 그냥 단순하게.. 아래처럼 했습니다...

    authorized_keys_for charlie do
      keys ''
      notifies :create "ruby_bloc[foo]"
    end

    ruby_block "foo" do
      block do
        # do something with ruby
      end
      action :nothing
    end

예.. 예상하시는데로.. 아무런 문제 없이 잘 될거라고 생각했지만, 역시나 안되었습니다(그러니까 이 글을 적고 있겠지요). 뭘까 뭘까? 하면서 다시 위 링크에 있는 문서를 다시 봤습니다.

> A definition is not a resource or a lightweight resource

녜.. definition은 resource가 아닙니다. notifies나 subscribes는 resource에만 동작합니다. 그러니 아무리해도 안되지요... definition은 진짜로 C언어의 #define 처럼 동작하는 녀석인가 봅니다. ㅎㅎ

 [1]: http://docs.opscode.com/essentials_cookbook_definitions.html