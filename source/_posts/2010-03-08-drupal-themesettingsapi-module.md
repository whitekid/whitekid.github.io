---
id: 31
title: drupal themesettingsapi module
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=31
permalink: /archives/31
dsq_thread_id:
  - 891063116
categories:
  - Uncategorized
tags:
  - drupal
  - ports
  - themes
  - todo
---

    # This is a shell archive. Save it in a file, remove anything before  
    # this line, and then unpack it by entering "sh file". Note, it may  
    # create directories; files and directories will be owned by you and  
    # have default permissions.  
    #  
    # This archive contains:  
    #  
    # www/drupal6-themesettingsapi  
    # www/drupal6-themesettingsapi/Makefile  
    # www/drupal6-themesettingsapi/distinfo  
    # www/drupal6-themesettingsapi/pkg-descr  
    #  
    echo c - www/drupal6-themesettingsapi  
    mkdir -p www/drupal6-themesettingsapi > /dev/null 2>&1  
    echo x - www/drupal6-themesettingsapi/Makefile  
    sed 's/^X//' >www/drupal6-themesettingsapi/Makefile << 'bcb04d505305fd2621e9906bb6352875&'  
    X# New ports collection makefile for: drupal6-themesettingsapi  
    X# Date created: 3 Mar 2010  
    X# Whom: <whitekid@gmail.com>  
    X#  
    X# $FreeBSD$  
    X#  
    X  
    XPORTNAME= themesettingsapi  
    XDISTVERSION= 6.x-1.4  
    XCATEGORIES= www  
    X  
    XMAINTAINER= <ports@FreeBSD.org>  
    XCOMMENT= Add custom settings to you theme for drupal.  
    X  
    XDRUPAL6_MODULE= yes  
    XMODULE_DIRS= translations  
    XMODULE_FILES= themesettingsapi.admin.inc themesettingsapi.info \  
    X themesettingsapi.module translations/de.po translations/ja.po \  
    X translations/themesettingsapi.pot  
    XDOC_FILES= CHANGELOG.txt README.txt LICENSE.txt  
    X  
    X.include <bsd.port.pre.mk>  
    X.include "${.CURDIR}/../../www/drupal5/bsd.drupal.mk"  
    X.include <bsd.port.post.mk>  
    bcb04d505305fd2621e9906bb6352875  
    echo x - www/drupal6-themesettingsapi/distinfo  
    sed 's/^X//' >www/drupal6-themesettingsapi/distinfo << '935c639b4a6c51408e999d94bc5cd788&'  
    XMD5 (drupal/themesettingsapi-6.x-1.4.tar.gz) = a2b5b5f156ea5b571be9f87638b9f471  
    XSHA256 (drupal/themesettingsapi-6.x-1.4.tar.gz) = 62be4dcc9ddae6930c3e985b0b0ffbb62584c9b6652a03202ae62053218315fa  
    XSIZE (drupal/themesettingsapi-6.x-1.4.tar.gz) = 9855  
    935c639b4a6c51408e999d94bc5cd788  
    echo x - www/drupal6-themesettingsapi/pkg-descr  
    sed 's/^X//' >www/drupal6-themesettingsapi/pkg-descr << 'afe1ea8baa59a0619c70f98199a10a5f'  
    XPathauto is a Drupal module which automatically generates path aliases for  
    Xvarious kinds of content (nodes, categories, users) without requiring the  
    Xuser to manually specify the path alias.  
    X  
    XWWW: <http://drupal.org/project/pathauto>  
    afe1ea8baa59a0619c70f98199a10a5f  
    exit  
    [/code]    