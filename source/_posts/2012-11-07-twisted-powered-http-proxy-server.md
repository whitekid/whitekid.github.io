---
id: 1086
title: twisted powered http proxy server
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1086
permalink: /archives/1086
dsq_thread_id:
  - 917079749
categories:
  - Uncategorized
tags:
  - python
  - twisted
---
역시 간단한 서버 작업 하는 데는 twisted 만한 것이 없습니다.  

    from twisted.web import proxy, http  
    from twisted.internet import reactor  
    from twisted.python import log  
    import sys

    log.startLogging(sys.stdout)

    class ProxyFactory(http.HTTPFactory):  
    protocol = proxy.Proxy

    reactor.listenTCP(8080, ProxyFactory())  
    reactor.run()  
