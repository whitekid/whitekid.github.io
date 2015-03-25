---
id: 1
title: www/wordpress-themes ports
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1
permalink: /archives/1
tags:
  - FreeBSD
  - ports
  - todo
  - WordPress
---
WordPress를 블로그로 사용하는 것을 검토하면서(동시에 Drupal도 검토하지만) 테마가 ports에 없군요. ports없는 설치는 FreBSD에서는 정말 참기 힘들 일이어서 간단하게 만들어 봅니다.

처음엔 간단히 하다가... 다음 기능들을 추가하다가 점점 복잡하게 되어버렸다.

  * pkg-plist 자동 생성
  * wordpress-mu 지원
  * 배포 파일에 숫자가 들어간것(P2, Greek Park 2) DISTFILES 처리

소스는 여기로 http://www.woosum.net/ports/www/wordpress-themes