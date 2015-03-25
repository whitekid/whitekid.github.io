---
id: 668
title: GeoIP python
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=668
permalink: /archives/668
dsq_thread_id:
  - 723474432
categories:
  - Uncategorized
tags:
  - GeoIP
  - python
---
<http://www.pointlessrants.com/2010/05/python-geoip-python-geoip-cities-tutorial/>

[code lang="python"]  
import GeoIP  
import socket

host = '[www.daum.net][1]'

gi = GeoIP.new(GeoIP.GEOIP\_MEMORY\_CACHE)  
print socket.gethostbyname(host), gi.country\_code\_by_name(host),

gi = GeoIP.open('GeoLiteCity.dat', GeoIP.GEOIP_STANDARD)  
city = gi.record\_by\_name(host)  
if city:  
print city['city'], city['region_name']  
[/code]

 [1]: http://www.daum.net