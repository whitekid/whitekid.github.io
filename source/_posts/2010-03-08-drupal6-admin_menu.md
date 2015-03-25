---
id: 40
title: drupal6-admin_menu
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=40
permalink: /archives/40
dsq_thread_id:
  - 764672667
categories:
  - Uncategorized
tags:
  - drupal
---
<pre>see <a href="http://www.woosum.net/ports/www/drupal6-admin_menu">http://www.woosum.net/ports/www/drupal6-admin_menu</a></p>


<p>
  [code lang="plain"]<br />
  # This is a shell archive.  Save it in a file, remove anything before<br />
  # this line, and then unpack it by entering "sh file".  Note, it may<br />
  # create directories; files and directories will be owned by you and<br />
  # have default permissions.<br />
  #<br />
  # This archive contains:<br />
  #<br />
  #       www/drupal6-admin_menu<br />
  #       www/drupal6-admin_menu/Makefile<br />
  #       www/drupal6-admin_menu/distinfo<br />
  #       www/drupal6-admin_menu/pkg-descr<br />
  #<br />
  echo c - www/drupal6-admin_menu<br />
  mkdir -p www/drupal6-admin_menu > /dev/null 2>&1<br />
  echo x - www/drupal6-admin_menu/Makefile<br />
  sed 's/^X//' >www/drupal6-admin_menu/Makefile << '6f47df969b7ef7c1e3f076d6bb57f21d'<br />
  X# New ports collection makefile for:   drupal6-admin_menu<br />
  X# Date created:                                23 Feb 2010<br />
  X# Whom:                                        <a href="mailto:whitekid@gmail.com">whitekid@gmail.com</a><br />
  X#<br />
  X# $FreeBSD$<br />
  X#<br />
  X<br />
  XPORTNAME=      admin_menu<br />
  XDISTVERSION=   6.x-1.5<br />
  XCATEGORIES=    www<br />
  X<br />
  XMAINTAINER=    <a href="mailto:ports@FreeBSD.org">ports@FreeBSD.org</a><br />
  XCOMMENT=       Theme-independent administration interface for drupal.<br />
  X<br />
  XDRUPAL6_MODULE=        yes<br />
  XMODULE_DIRS=   images tests translations<br />
  XMODULE_FILES=  admin_menu-rtl.css admin_menu.css admin_menu.inc \<br />
  X               admin_menu.info admin_menu.install admin_menu.js \<br />
  X               admin_menu.module images/arrow-rtl.png images/arrow.png \<br />
  X               images/bkg.png images/bkg_tab.png images/icon_users.png \<br />
  X               tests/admin_menu.test translations/admin_menu.pot \<br />
  X               translations/da.po translations/de.po translations/es.po \<br />
  X               translations/fr.po translations/hu.po translations/ja.po \<br />
  X               translations/nl.po translations/pt-br.po translations/ru.po \<br />
  X               translations/sv.po<br />
  XDOC_FILES=     CHANGELOG.txt README.txt LICENSE.txt<br />
  X<br />
  X.include <bsd.port.pre.mk><br />
  X.include "${.CURDIR}/../../www/drupal5/bsd.drupal.mk"<br />
  X.include <bsd.port.post.mk><br />
  6f47df969b7ef7c1e3f076d6bb57f21d<br />
  echo x - www/drupal6-admin_menu/distinfo<br />
  sed 's/^X//' >www/drupal6-admin_menu/distinfo << '5a46b96780d00134834a4fb436cad805'<br />
  XMD5 (drupal/admin_menu-6.x-1.5.tar.gz) = efa340632c9fd0b9a964671bed27a722<br />
  XSHA256 (drupal/admin_menu-6.x-1.5.tar.gz) = 41bef68b0599f781dfe4887055979a677e09b5be3c2a020c3534b95292da3b26<br />
  XSIZE (drupal/admin_menu-6.x-1.5.tar.gz) = 45284<br />
  5a46b96780d00134834a4fb436cad805<br />
  echo x - www/drupal6-admin_menu/pkg-descr<br />
  sed 's/^X//' >www/drupal6-admin_menu/pkg-descr << '4d3b06cf926ae9928613690e8cb7eb92'<br />
  XAdministration menu module provides a theme-independent administration interface<br />
  X(aka. "navigation", "back-end") for Drupal. It's a helper for novice Drupal<br />
  Xusers coming from other CMS, a real time-saver for Drupal site administrators,<br />
  Xand definitely a must for Drupal developers and site builders.<br />
  X<br />
  XWWW: <a href="http://drupal.org/project/admin_menu">http://drupal.org/project/admin_menu</a><br />
  4d3b06cf926ae9928613690e8cb7eb92<br />
  exit<br />
  [/code]
</p>