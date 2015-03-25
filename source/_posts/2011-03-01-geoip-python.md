---
id: 668
title: GeoIP python
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=668
permalink: /archives/668
dsq_thread_id:
  - 723474432
tags:
  - GeoIP
  - python
---
http://www.pointlessrants.com/2010/05/python-geoip-python-geoip-cities-tutorial/

    import GeoIP
    import socket

    host = 'www.daum.net'

    gi = GeoIP.new(GeoIP.GEOIP_MEMORY_CACHE)
    print socket.gethostbyname(host), gi.country_code_by_name(host),

    gi = GeoIP.open('GeoLiteCity.dat', GeoIP.GEOIP_STANDARD)
    city = gi.record_by_name(host)
    if city:
    print city['city'], city['region_name']
