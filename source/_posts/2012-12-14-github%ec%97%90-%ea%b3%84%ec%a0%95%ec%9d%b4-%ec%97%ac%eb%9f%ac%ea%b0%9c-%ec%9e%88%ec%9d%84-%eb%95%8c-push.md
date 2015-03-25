---
id: 1189
title: github에 계정이 여러개 있을 때.. push
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1189
permalink: /archives/1189
dsq_thread_id:
  - 974181040
---
github에 계정이 2개 있습니다. 하나는 회사것 하나는 개인것... 물론 public_key도 다르겠지요..
별 생각없이 사용하면, 개인 레포지트리도 회사의 계정으로 접근을 합니다. 최근까지도 신경안쓰고 있었다가, 개인 레포에 push할 것이 있어서 했는데, 에러가 나더군요.

어떻게 할까.. 고민하다가.. ssh_config를 조금 손보는 것으로 처리했습니다.

    Host github.com
    User git
    HostName github.com
    IdentityFile ~/.ssh/company_id_key.rsa

    Host whitekid.github.com
    User git
    HostName github.com
    IdentityFile ~/.ssh/personal_key_rsa

위처럼 ~/.ssh/config를 적당히 손봅니다. 이제 ssh github.com하면 첫번째 설정으로 접근하고, ssh whitekid.github.com 하면 두번째 설정으로 접근하게 됩니다.

이렇게 하면 ssh가 구분하여 호스트를 설정하는 것은 되었고 git repository에서는 위의 설정대로 .git/config 파일에서 개인 레포지트르의 호스트 설정을 살짝 바꿔주면 됩니다.

    [remote "origin"]
    fetch = +refs/heads/*:refs/remotes/origin/*
    url = <git@whitekid.github.com>:whitekid/openstack-chef.git

이제는 어디서든 push 해도 햇갈일 일이 없음~