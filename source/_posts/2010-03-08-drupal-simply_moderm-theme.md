---
id: 29
title: drupal simply_moderm theme
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=29
permalink: /archives/29
dsq_thread_id:
  - 786824996
tags:
  - drupal
---
[themesettingsapi-module][1] 필요함

    # This is a shell archive. Save it in a file, remove anything before
    # this line, and then unpack it by entering "sh file". Note, it may
    # create directories; files and directories will be owned by you and
    # have default permissions.
    #
    # This archive contains:
    #
    # www/drupal6-simply_modern
    # www/drupal6-simply_modern/Makefile
    # www/drupal6-simply_modern/distinfo
    # www/drupal6-simply_modern/pkg-descr
    #
    echo c - www/drupal6-simply_modern
    mkdir -p www/drupal6-simply_modern > /dev/null 2>&1
    echo x - www/drupal6-simply_modern/Makefile
    sed 's/^X//' >www/drupal6-simply_modern/Makefile << 'b8cdc2bb06814f1bbeab576f974258a1&'
    X# New ports collection makefile for: drupal6-simply_modern
    X# Date created: 3 Mar 2010
    X# Whom: <whitekid@gmail.com>
    X#
    X# $FreeBSD$
    X#
    X
    XPORTNAME= simply_modern
    XDISTVERSION= 6.x-3.1
    XCATEGORIES= www
    X
    XMAINTAINER= <ports@FreeBSD.org>
    XCOMMENT= The simply modern drupal theme
    X
    XRUN_DEPENDS= drupal6-themesettingsapi>=0:${PORTSDIR}/www/drupal6-themesettingsapi
    X
    XDRUPAL6_THEME= yes
    XMODULE_DIRS= css/images/tabs css/images css js images/black images/blue \
    X images/copper images/green images/red images translations
    X
    XMODULE_FILES= block.tpl.php css/default.css css/ie6.css css/ie7.css \
    X css/images/bg_pattern_brown.jpg css/images/header.gif \
    X css/images/screenshot.png css/images/tabs/tab-bar.png \
    X css/images/tabs/tab-left-ie6.png css/images/tabs/tab-left.png \
    X css/images/tabs/tab-right-ie6.png \
    X css/images/tabs/tab-right.png \
    X css/images/tabs/tab-secondary-bg.png \
    X css/images/tabs/tab-secondary.png \
    X css/images/top_gradient_dark.jpg \
    X css/images/top_gradient_light.jpg css/layout.css css/print.css \
    X css/style.css css/tabs.css favicon.ico \
    X images/bg_pattern_brown.jpg images/black/BottomBG.png \
    X images/black/MastBG.png images/black/MenuDiv.png \
    X images/black/RoopleThemeLogo.png images/black/header.gif \
    X images/black/logo.png images/blue/BottomBG.png \
    X images/blue/MastBG.png images/blue/MenuDiv.png \
    X images/blue/RoopleThemeLogo.png images/blue/header.gif \
    X images/blue/logo.png images/copper/BottomBG.png \
    X images/copper/MastBG.png images/copper/MenuDiv.png \
    X images/copper/RoopleThemeLogo.png images/copper/header.gif \
    X images/copper/logo.png images/favicon.ico \
    X images/green/BottomBG.png images/green/MastBG.png \
    X images/green/MenuDiv.png images/green/RoopleThemeLogo.png \
    X images/green/header.gif images/green/logo.png \
    X images/menu_arrow.png images/page_bg_left.png \
    X images/page_bg_right.png images/red/BottomBG.png \
    X images/red/MastBG.png images/red/MenuDiv.png \
    X images/red/RoopleThemeLogo.png images/red/header.gif \
    X images/red/logo.png images/top_gradient_dark.jpg \
    X images/top_gradient_light.jpg js/jquery.pngFix.js \
    X js/pickstyle.js js/suckerfish.js logo.png node.tpl.php \
    X page.tpl.php simply_modern.info template.php \
    X theme-settings.php translations/hu.po
    XDOC_FILES= LICENSE.txt
    X
    X.include <bsd.port.pre.mk>
    X.include "${.CURDIR}/../../www/drupal5/bsd.drupal.mk"
    X.include <bsd.port.post.mk>
    b8cdc2bb06814f1bbeab576f974258a1
    echo x - www/drupal6-simply_modern/distinfo
    sed 's/^X//' >www/drupal6-simply_modern/distinfo << 'c3558f3948d3ec2f8921c637916fff39&'
    XMD5 (drupal/simply_modern-6.x-3.1.tar.gz) = 63ec13e369262adbfe20460838c639f6
    XSHA256 (drupal/simply_modern-6.x-3.1.tar.gz) = 193eff35afe3cee7c2270bd7900c04bf4e4a05e2d8e3b579de3ff1dca525ea1a
    XSIZE (drupal/simply_modern-6.x-3.1.tar.gz) = 66612
    c3558f3948d3ec2f8921c637916fff39
    echo x - www/drupal6-simply_modern/pkg-descr
    sed 's/^X//' >www/drupal6-simply_modern/pkg-descr << '12a37c37db8f35cffa85aa6be8a15627&'
    XSimply Modern is a table-less, CSS driven design sporting an advanced feature
    Xset.
    X
    XWWW: http://drupal.org/project/simply_modern
    12a37c37db8f35cffa85aa6be8a15627
    exit

 [1]: /2010/03/drupal-themesettingsapi-module/