---
id: 684
title: python-mcrypt 사용 예
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=684
permalink: /archives/684
dsq_thread_id:
  - 735039029
tags:
  - mcrypt
  - python
---
[python-mcrypt][1]은 문서가 없다. 포함된 test.py를 보면 되는데... 좀 길고..

    #! -*- encoding: utf8 -*-
    import base64
    import urllib
    import mcrypt
    import random
    import string

    def random_string(length):
            return ''.join([random.choice(string.ascii_letters + string.digits) for x in range(length)])

    chiper_key = random_string(16)

    # encode
    def encrypt(data):
            m = mcrypt.MCRYPT('rijndael-256', 'cfb')
            iv_size = m.get_iv_size()
            iv = random_string(iv_size)

            m.init(chiper_key, iv)
            return base64.b64encode(m.encrypt(iv + data))


    # decode
    def decrypt(data):
            data = base64.b64decode(data)
            m = mcrypt.MCRYPT('rijndael-256', 'cfb')
            iv_size = m.get_iv_size()
            iv = data[:iv_size]
            encrypted = data[iv_size:]
            m.init(chiper_key, iv)
            return m.decrypt(encrypted)

    data = '안녕하세요'
    assert data == decrypt(encrypt(data))


 [1]: http://niemeyer.net/python-mcrypt