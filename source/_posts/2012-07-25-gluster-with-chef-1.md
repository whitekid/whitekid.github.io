---
id: 949
title: 'gluster with chef #1'
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=949
permalink: /archives/949
dsq_thread_id:
  - 778393475
tags:
  - chef
  - glusterfs
---
공부삼아서 Chef로 Gluster를 셋업하는 것(https://github.com/whitekid/chef-glusterfs) 해 봤는데, 역시나 공부삼아 코멘트 적어봅니다.

물론 당연히도 저는 Chef와 Ruby를 처음 다르기에 정석과는 많이 다를 수 있고, 여기에서 이야기하는 것들이 틀릴 수 도 있습니다. ㅎㅎ..

https://github.com/whitekid/chef-glusterfs 는 chef에서 사용하는 cookbook입니다. cookbook 즉 요리책은 형상관리할 대상을 어떻게 요리할 지 적어 놓은 것이죠. 이 cookbook안에 recipe가 있으며, 이 recipe를 작성하여 하나의 인프라를 관리합니다.

recipes 디렉토리를 보시면 client.rb, server.rb, default.rb 라는 파일이 있습니다. 이게 recipe이며, 이것들이 여러개 모여서 glusterfs 클라이언트와 서버를 관리하는 cookbook이 되는 것이죠. 이름에서 보이다시피 client.rb는 glusterfs 클라이언트를 설치하고 서버와 연결하여 마운트하는 recipe이고, server.rb는 glusterfs 클러스터를 구성하고 volume을 생성하는 recipe입니다.

우선 간단한 recipes/client.rb부터 봅니다.

    package "glusterfs-fuse" do
      action :install
    end

여기서 ":install" 이 뭘까요?

저는 한참 고민했습니다. 엇듯 봐서는 method 같기도 하고, action이 method이면 :install은 parameter인 것 같기도 하고.. 처음에는 우선 려러니 하고 넘어갔었는데, 찾아보니 :install은 문자열 "install"의 symbol이라고 합니다. 문자열 reference죠.

    irb> :install.equal? :install
    => true

로 나옵니다. 여기서 equals?는 값 비교가 아닌 object 비교, 즉 같은 메모리를 점유하고 있는 오브젝트인가를 검사하는 것인데 같이 나오는 으로 보면 같은 object이죠.

python의 immutable과 비슷하다고 봐야합니다. ruby symbol도 immutable입니다.

> "Symbols are a way to represent strings and names in ruby."

라 설명하고 있네요.

근데 그럼 action은 뭘까요? 이건.. attribute입니다. 즉 package 리소스의 action attribute를 "install"로 설정하는 것입니다.

package라는 리소스를 이용해서 gluster-fuse 패키지를 설치합니다. package는 각 chef node의 OS에 맞게 패키지를 설치합니다. 따라서 CentOS는 yum을 Ubuntu는 apt-get을 FreeBSD는 ports를 이용하여 설치를 합니다.

chef에서 사용되는 리소스는 opscode의 리소스 패이지(http://wiki.opscode.com/display/chef/Resources)를 보시기 바랍니다. 아주 자세히 설명되어 있습니다.

당연히 gluster-fuse는 CentOS에서 있는 패키지 이름입니다. 만일 Ubuntu를 사용한다면 다른 이름을 넣어주겠죠.

이렇게 각 노드의 특성에 따라서 달라지는 것들은 attribute에 설정합니다. 아마 attrbiute/default.rb 파일은 다음처럼 될 겁니다.

    case platform do
      when "ubutu", "debian"
        default[:node][:gluster_client_package] = "gluster-client"
      when "centos", "redhat"
        default[:node][:gluster_client_package] = "gluster-fuse"
    end

recipes는 아래처럼 사용합니다.

    package node[:ntp][:gluster_client_package] do
      action :install
    end

근데 이런 node 특성에 따른 설정을 attribute에서만 꼭 둬야하냐? 라고 말하면 recipe에 둬도 됩니다. 편하실대로 하는는 것이 좋겠습니다. 예전에 잠깐 듣기론 recipe에 넣는 것이 더 편하는 이야기를 들은 것 같아요.

클라이언트 패키지를 설치했으므로 glusterfs 클러스터에 마운트합니다. glusterfs 클러스터는 server recipe에서 셋업을 진행했고 attributes/server.rb에서 설정한 peer의 첫번째로 마운트 하도록 하겠습니다.

    node[:glusterfs][:client][:mount].each do |volume, mount_to|  # ---- [1]
      if not File.directory?(mount_to)                      # ---- [2]
        directory mount_to do
          action :create
          recursive true
        end
      end

      # mount -t glusterfs -o log-level=WARNING,log-file=/var/log/gluster.log \
      #       10.200.1.11:/test /mnt
      server = node[:glusterfs][:server][:peers][0]
      mount mount_to do                                    # ---- [3]
        device "#{server}:/#{volume}"
        options "log-level=WARNING,log-file=/var/log/gluster.log"
      end
    end

----[1] glusterfs의 볼륨은 여러개 있을 수 있습니다. 따라서 각각의 볼륨에 대해서 glusterfs 마운트를 진행합니다. recipes/server.rb에 아래처럼 array로 정의되어 있습니다.

    default[:glusterfs][:server][:volumes] = ["test"]

array의 each를 이용하여 모든 element를 돌면서 주어진 코드 블럭을 수행합니다. ruby 스타일의 언어를 처음 접하면 어색하겠지만, for문과 같다고 보시면 됩니다.

    for volume, mount in node['glusterfs']['client']['mount']:
        bla bla

python 스타일로 하면 위와 같은데.. python은 for 안의 실행하는 문장이 같은 for 문에 속하는 scope에 속하는 반면에, ruby에서는 do ~ end 가 코드 블럭이 되어서 each 함수의 파라미터로 전달이 되고 each 안에서 다시 호출되는 방식입니다. 아마도 javascript의 이벤트 핸들러를 작성해 봤다면 그것과 비슷하도고 보시면 됩니다.

----[2] 마운트 포인트 디렉토리가 없는 경우는 새로 만들어 줍니다. 디렉토리 생성은 directory라는 리소스를 통해서 생성하며 :create action으로 수행됩니다.

---- [3] mount 리소스를 이용해서 gluster 서버로 마운트 합니다. 위의 주석으로 달린 명령과 동일한 과정을 수행합니다.

그리고 어찌보면 요상한 "#{server}:/#{volume}" 문자열이 나오는데, sh로 치면 "${server}:/${volume}"과 동일하며 python으로 치면 "%s:/%s" % (server, volume)와 동일한 문자열 치환 입니다. python 스타일보다 훨씬 직관적으로 보기 좋습니다. 그렇다고 python "%{server}s:/%{volume}s" % locals() 가 있나고 하신다면.. 그래도 ruby style이 더 좋습니다..

음.. 대충 적으려고 계획했는데.. 글 재주도 없고 모르는 것 적으려니 이것저것 찾아보면서 해서 오래걸리네요.. 오늘은 이만하고 다음에 나머지 진행하겠습니다... 이거 참 하루 저녁 작업하고 설명하는데 몇일 걸리겠네요.. ㅡㅡ; 게다가 서버쪽은 더 복잡해 큰일군요.