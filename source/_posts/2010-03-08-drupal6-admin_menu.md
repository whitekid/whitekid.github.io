---
id: 40
title: drupal6-admin_menu
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=40
permalink: /archives/40
dsq_thread_id:
  - 764672667
tags:
  - drupal
---
see http://www.woosum.net/ports/www/drupal6-admin_menu

    # This is a shell archive.  Save it in a file, remove anything before
    # this line, and then unpack it by entering "sh file".  Note, it may
    # create directories; files and directories will be owned by you and
    # have default permissions.
    #
    # This archive contains:
    #
    #       www/drupal6-admin_menu
    #       www/drupal6-admin_menu/Makefile
    #       www/drupal6-admin_menu/distinfo
    #       www/drupal6-admin_menu/pkg-descr
    #
    echo c - www/drupal6-admin_menu
    mkdir -p www/drupal6-admin_menu > /dev/null 2>&1
    echo x - www/drupal6-admin_menu/Makefile
    sed 's/^X//' >www/drupal6-admin_menu/Makefile << '6f47df969b7ef7c1e3f076d6bb57f21d'
    X# New ports collection makefile for:   drupal6-admin_menu
    X# Date created:                                23 Feb 2010
    X# Whom:                                        whitekid@gmail.com
    X#
    X# $FreeBSD$
    X#
    X
    XPORTNAME=      admin_menu
    XDISTVERSION=   6.x-1.5
    XCATEGORIES=    www
    X
    XMAINTAINER=    ports@FreeBSD.org
    XCOMMENT=       Theme-independent administration interface for drupal.
    X
    XDRUPAL6_MODULE=        yes
    XMODULE_DIRS=   images tests translations
    XMODULE_FILES=  admin_menu-rtl.css admin_menu.css admin_menu.inc \
    X               admin_menu.info admin_menu.install admin_menu.js \
    X               admin_menu.module images/arrow-rtl.png images/arrow.png \
    X               images/bkg.png images/bkg_tab.png images/icon_users.png \
    X               tests/admin_menu.test translations/admin_menu.pot \
    X               translations/da.po translations/de.po translations/es.po \
    X               translations/fr.po translations/hu.po translations/ja.po \
    X               translations/nl.po translations/pt-br.po translations/ru.po \
    X               translations/sv.po
    XDOC_FILES=     CHANGELOG.txt README.txt LICENSE.txt
    X
    X.include <bsd.port.pre.mk>
    X.include "${.CURDIR}/../../www/drupal5/bsd.drupal.mk"
    X.include <bsd.port.post.mk>
    6f47df969b7ef7c1e3f076d6bb57f21d
    echo x - www/drupal6-admin_menu/distinfo
    sed 's/^X//' >www/drupal6-admin_menu/distinfo << '5a46b96780d00134834a4fb436cad805'
    XMD5 (drupal/admin_menu-6.x-1.5.tar.gz) = efa340632c9fd0b9a964671bed27a722
    XSHA256 (drupal/admin_menu-6.x-1.5.tar.gz) = 41bef68b0599f781dfe4887055979a677e09b5be3c2a020c3534b95292da3b26
    XSIZE (drupal/admin_menu-6.x-1.5.tar.gz) = 45284
    5a46b96780d00134834a4fb436cad805
    echo x - www/drupal6-admin_menu/pkg-descr
    sed 's/^X//' >www/drupal6-admin_menu/pkg-descr << '4d3b06cf926ae9928613690e8cb7eb92'
    XAdministration menu module provides a theme-independent administration interface
    X(aka. "navigation", "back-end") for Drupal. It's a helper for novice Drupal
    Xusers coming from other CMS, a real time-saver for Drupal site administrators,
    Xand definitely a must for Drupal developers and site builders.
    X
    XWWW: http://drupal.org/project/admin_menu
    4d3b06cf926ae9928613690e8cb7eb92
    exit
