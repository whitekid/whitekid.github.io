---
id: 43
title: www/drupal6-r4032login
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=43
permalink: /archives/43
dsq_thread_id:
  - 767770657
categories:
  - Uncategorized
tags:
  - drupal
  - ports
---

    # This is a shell archive. Save it in a file, remove anything before  
    # this line, and then unpack it by entering "sh file". Note, it may  
    # create directories; files and directories will be owned by you and  
    # have default permissions.  
    #  
    # This archive contains:  
    #  
    # www/drupal6-r4032login  
    # www/drupal6-r4032login/Makefile  
    # www/drupal6-r4032login/distinfo  
    # www/drupal6-r4032login/pkg-descr  
    #  
    echo c - www/drupal6-r4032login  
    mkdir -p www/drupal6-r4032login > /dev/null 2>&1  
    echo x - www/drupal6-r4032login/Makefile  
    sed 's/^X//' >www/drupal6-r4032login/Makefile << '5cde0bc78d03feafc52b54f67bed9395&'  
    X# New ports collection makefile for: drupal6-r4032login  
    X# Date created: 9 Mar 2010  
    X# Whom: <whitekid@gmail.com>  
    X#  
    X# $FreeBSD$  
    X#  
    X  
    XPORTNAME= r4032login  
    XDISTVERSION= 6.x-1.2  
    XCATEGORIES= www  
    X  
    XMAINTAINER= whitekid2gmail.com  
    XCOMMENT= r4032login drupal module  
    X  
    XDRUPAL6_MODULE= yes  
    XMODULE_FILES= r4032login.module r4032login.info r4032login.install  
    XMODULE_DIRS= translations  
    XDOC_FILES= README.TXT LICENSE.txt  
    X  
    X.include <bsd.port.pre.mk>  
    X.include "${.CURDIR}/../../www/drupal5/bsd.drupal.mk"  
    X.include <bsd.port.post.mk>  
    5cde0bc78d03feafc52b54f67bed9395  
    echo x - www/drupal6-r4032login/distinfo  
    sed 's/^X//' >www/drupal6-r4032login/distinfo << 'a6f822bd1235d42329ab9498b569e634&'  
    XMD5 (drupal/r4032login-6.x-1.2.tar.gz) = 106cbd4e6e87d344c10f510856e70836  
    XSHA256 (drupal/r4032login-6.x-1.2.tar.gz) = 62c20d6d7a3126ef22e5feed5872a920f44ecfcab15d6f0883d04a6fad9e6276  
    XSIZE (drupal/r4032login-6.x-1.2.tar.gz) = 8868  
    a6f822bd1235d42329ab9498b569e634  
    echo x - www/drupal6-r4032login/pkg-descr  
    sed 's/^X//' >www/drupal6-r4032login/pkg-descr << 'f8bb830a87e7c75f347ab592e8dd9528&'  
    XRedirect the HTTP 403 error page to the Drupal /user/login page with an  
    Xoptional message.  
    X  
    XWWW: <http://drupal.org/project/r4032login>  
    f8bb830a87e7c75f347ab592e8dd9528  
    exit  
