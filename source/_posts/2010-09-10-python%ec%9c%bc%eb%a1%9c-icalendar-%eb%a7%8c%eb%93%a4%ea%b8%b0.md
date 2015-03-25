---
id: 422
title: Python으로 iCalendar 만들기
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=422
permalink: /archives/422
dsq_thread_id:
  - 752225743
tags:
  - iCalendar
  - python
  - vobject
---
Python으로 [iCalendar][1]를 만드는건 [vobject][2]을 이용하면됩니다. 다른 iCalendar 라이브러리인 [py-iCalendar][3]도 있는데, 이벤트 각 필드에 parameter를 주는데 문제가 있어서 잘 안됩니다.

    # -*- encoding: utf8 -*-
    import vobject
    from datetime import datetime

    cal = vobject.iCalendar()
    event = cal.add('vevent')
    event.add('summary').value = u'이벤트 하나'
    event.add('description').value = u'이벤트 하나\n\n모두 참석해주세요...'
    event.add('dtstart').value = datetime(2010, 9, 9, 18, 0, 0)
    event.add('dtend').value = datetime(2010, 9, 9, 20, 0, 0)
    event.add('dtstamp').value = datetime(2010, 9, 9, 18, 0, 0)
    event.add('location').value = u'회의실'

    o = event.add('organizer')
    o.value = u'<organizer@gmail.com>'
    o.params['CN'] = [u'오거나이저']

    o = event.add('attendee')
    o.value = u'<must@gmail.com>'
    o.params['CN'] = [u'필수 참석자']
    o.params['ROLE'] = [u'REQ-PARTICIPANT']

    o = event.add('attendee')
    o.value = u'<optinal@gmail.com>'
    o.params['CN'] = [u'옵션 참석자']
    o.params['ROLE'] = [u'OPT-PARTICIPANT']

    o = event.add('attendee')
    o.value = u'<optinal1@gmail.com>'
    o.params['CN'] = [u'옵션 참석자1&']
    o.params['ROLE'] = [u'OPT-PARTICIPANT']

    file('example.ics', 'wb').write(cal.serialize())

이코드로 만들어진 파일은 아래와 같습니다.

    BEGIN:VCALENDAR
    VERSION:2.0
    PRODID:-//PYVOBJECT//NONSGML Version 1//EN
    BEGIN:VEVENT
    UID:20100910T025017Z-99461@woosum.net
    DTSTART:20100909T180000
    DTEND:20100909T200000
    ATTENDEE;ROLE=REQ-PARTICIPANT;CN=필수 참석자:must@gmail.com
    ATTENDEE;ROLE=OPT-PARTICIPANT;CN=옵션 참석자:optinal@gmail.com
    ATTENDEE;ROLE=OPT-PARTICIPANT;CN=옵션 참석자1:optinal1@gmail.com
    DESCRIPTION:이벤트 하나\n\n모두 참석해주세요...
    DTSTAMP:20100909T180000
    LOCATION:회의실
    ORGANIZER;CN=오거나이저:organizer@gmail.com
    SUMMARY:이벤트 하나
    END:VEVENT
    END:VCALENDAR

이제 이 파일을 Outlook 인터넷 일정으로 등록하여 공유할 수 있고, 다른 여러 용도로 사용할 수 있겠지요.

 [1]: http://tools.ietf.org/html/rfc2445
 [2]: http://vobject.skyhouseconsulting.com/
 [3]: http://codespeak.net/icalendar/