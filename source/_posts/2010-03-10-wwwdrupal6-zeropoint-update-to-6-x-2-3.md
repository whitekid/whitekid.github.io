---
id: 45
title: www/drupal6-zeropoint update to 6.x-2.3
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=45
permalink: /archives/45
dsq_thread_id:
  - 858401677
tags:
  - drupal
  - ports
  - pr-sent
  - theme
  - todo
  - zeropoint
---

    diff -ruN drupal6-zeropoint.orig/Makefile drupal6-zeropoint/Makefile
    --- drupal6-zeropoint.orig/Makefile 2010-03-10 07:25:31.000000000 +0900
    +++ drupal6-zeropoint/Makefile 2010-03-10 02:31:11.000000000 +0900
    @@ -6,7 +6,7 @@
    #

    PORTNAME= zeropoint
    -DISTVERSION= 6.x-1.10
    +DISTVERSION= 6.x-2.3
    CATEGORIES= www

    MAINTAINER= <whitekid@gmail.com>
    @@ -17,7 +17,6 @@
    MODULE_DIRS= _custom/headerimg _custom css images/all images/ink \
    images/icons/blocks images/icons images/ivy images/lime \
    images/nature images/sangue images/sky images js
    -
    MODULE_FILES= _custom/custom-style.css _custom/headerimg/credits.txt \
    _custom/headerimg/ephemeral_a.jpg \
    _custom/headerimg/ephemeral_b.jpg \
    @@ -25,19 +24,21 @@
    _custom/headerimg/ephemeral_d.jpg \
    _custom/headerimg/ephemeral_e.jpg _custom/headerimg/rotate.php \
    block.tpl.php box.tpl.php comment-wrapper-forum.tpl.php \
    - comment-wrapper.tpl.php comment.tpl.php css/ie6.css css/ie7.css \
    - css/iepngfix.htc css/ink.css css/ivy.css css/lime.css \
    - css/nature.css css/print.css css/round.css css/sangue.css \
    - css/sky.css css/style-zero-rtl.css css/style-zero.css \
    - favicon.ico images/all/_brand.png images/all/_sidebarimg.png \
    - images/all/fill_left.png images/all/fill_right.png \
    - images/all/forum-container.jpg images/all/forum-link.png \
    - images/all/icon-comment.png images/all/ltr_forum-container.jpg \
    + comment-wrapper.tpl.php comment.tpl.php css/ie6.css \
    + css/ie7.css css/iepngfix.htc css/ink.css css/ivy.css \
    + css/lime.css css/nature.css css/print.css css/round.css \
    + css/sangue.css css/sky.css css/style-zero-rtl.css \
    + css/style-zero.css favicon.ico images/all/_brand.png \
    + images/all/_sidebarimg.png images/all/fill_left.png \
    + images/all/fill_right.png images/all/forum-container.jpg \
    + images/all/forum-link.png images/all/icon-comment.png \
    + images/all/ltr_forum-container.jpg \
    images/all/ltr_forum-link.png images/all/ltr_icon-comment.png \
    - images/all/ltr_menu-collapsed.png images/all/menu-collapsed.png \
    - images/all/menu-expanded.png images/all/menu-leaf.png \
    - images/all/menu-up.png images/all/spacer.gif images/arr.png \
    - images/fill_block.png images/fill_block2.png images/fill_top.png \
    + images/all/ltr_menu-collapsed.png \
    + images/all/menu-collapsed.png images/all/menu-expanded.png \
    + images/all/menu-leaf.png images/all/menu-up.png \
    + images/all/spacer.gif images/arr.png images/fill_block.png \
    + images/fill_block2.png images/fill_top.png \
    images/fill_top_left.png images/fill_top_right.png \
    images/icons/attach.png images/icons/blocks/icon_01_32.png \
    images/icons/blocks/icon_01_48.png \
    @@ -137,12 +138,13 @@
    images/sky/fill_top_right.png js/preloadCssImages.jQuery_v5.js \
    js/suckerfish.js logo.png maintenance-page.tpl.php \
    node-product.tpl.php node-webform.tpl.php node.tpl.php \
    - page.tpl.php screenshot-drupal.org.jpg screenshot-drupal.org.png \
    - screenshot.png search-result.tpl.php search-theme-form.tpl.php \
    - template.php theme-settings.php views-view.tpl.php \
    - zeropoint.info
    + page.tpl.php screenshot-drupal.org.jpg \
    + screenshot-drupal.org.png screenshot.png search-result.tpl.php \
    + search-theme-form.tpl.php template.php theme-settings.php \
    + views-view.tpl.php zeropoint.info
    DOC_FILES= CHANGELOG.txt LICENSE.txt README.txt

    .include <bsd.port.pre.mk>
    .include "${.CURDIR}/../../www/drupal5/bsd.drupal.mk"
    .include <bsd.port.post.mk>
    +
    diff -ruN drupal6-zeropoint.orig/distinfo drupal6-zeropoint/distinfo
    --- drupal6-zeropoint.orig/distinfo 2010-03-10 07:25:31.000000000 +0900
    +++ drupal6-zeropoint/distinfo 2010-03-10 02:26:33.000000000 +0900
    @@ -1,3 +1,3 @@
    -MD5 (drupal/zeropoint-6.x-1.10.tar.gz) = dbaa5fc01b0bc79bb5459b3dc3fe1c79
    -SHA256 (drupal/zeropoint-6.x-1.10.tar.gz) = c6c5f159f0d3519dc9fa254cc9ddbaea399731de7c06ada0aa8cbeb75cca539c
    -SIZE (drupal/zeropoint-6.x-1.10.tar.gz) = 384303
    +MD5 (drupal/zeropoint-6.x-2.3.tar.gz) = 510bca2664e7cb13735af87b64a4f2d0
    +SHA256 (drupal/zeropoint-6.x-2.3.tar.gz) = dc6eec4c57067ec98ec2b5b09187bf65383250d4fcb3cbfb79a6fd8e7dfd5adc
    +SIZE (drupal/zeropoint-6.x-2.3.tar.gz) = 384299

http://www.freebsd.org/cgi/query-pr.cgi?pr=144610