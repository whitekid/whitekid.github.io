---
id: 14
title: some drupal modules to update
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=14
permalink: /archives/14
tags:
  - drupal
  - ports
---
## www/drupal6-print

PDF 생성을 위해서 http://tcpdf.sourceforge.net/ 가 필요하다.
근데 이게 한글이 될까? 폰트도 없는데... 음 우선 해보고..

    diff -ruN drupal6-print.orig/Makefile drupal6-print/Makefile
    --- drupal6-print.orig/Makefile 2010-02-05 20:44:48.000000000 +0900
    +++ drupal6-print/Makefile  2010-03-03 00:36:38.000000000 +0900
    @@ -5,8 +5,7 @@
     # $FreeBSD: ports/www/drupal6-print/Makefile,v 1.2 2010/02/05 11:44:48 dinoex Exp $

     PORTNAME=  print
    -DISTVERSION= 6.x-1.0-rc5
    -PORTREVISION=  1
    +DISTVERSION= 6.x-1.10
     CATEGORIES=  www
     MASTER_SITES=  http://ftp.drupal.org/files/projects/

    @@ -14,14 +13,17 @@
     COMMENT= Drupal 6 module that add printer-friendly pages

     DRUPAL6_MODULE=  yes
    -MODULE_DIRS= icons translations
    +MODULE_DIRS= css icons print_mail print_pdf translations
     MODULE_FILES=  icons/mail_icon.gif icons/pdf_icon.gif icons/print_icon.gif \
    -   print-rtl.css print.admin.inc print.css print.info \
    -   print.install print.module print.pages.inc print.pdf.inc \
    -   print.tpl.php print_mail.admin.inc print_mail.inc \
    -   print_mail.info print_mail.install print_mail.module \
    -   print_pdf.admin.inc print_pdf.info print_pdf.install \
    -   print_pdf.module \
    +   css/print-rtl.css css/print.css css/printlinks-rtl.css css/printlinks.css \
    +   print.admin.inc print.info print.install print.module \
    +   print.pages.inc print.tpl.php README.txt \
    +   print_mail/print_mail.admin.inc print_mail/print_mail.inc \
    +   print_mail/print_mail.info print_mail/print_mail.install \
    +   print_mail/print_mail.module print_pdf/print_pdf.admin.inc \
    +   print_pdf/print_pdf.class.inc print_pdf/print_pdf.class_php4.inc \
    +   print_pdf/print_pdf.info print_pdf/print_pdf.install \
    +   print_pdf/print_pdf.module print_pdf/print_pdf.pages.inc \
        translations/print.pot translations/ca.po \
        translations/cs.po translations/da.po translations/de.po \
        translations/es.po translations/fi.po translations/fr.po \
    @@ -30,7 +32,7 @@
        translations/pl.po translations/pt-br.po \
        translations/pt-pt.po translations/ro.po \
        translations/ru.po translations/sk.po translations/sv.po \
    -   translations/tr.po translations/uk.po
    +   translations/tr.po translations/uk.po translations/vi.po
     DOC_FILES= CHANGELOG.txt CREDITS.txt INSTALL.txt \
        LICENSE.txt MAINTAINERS.txt README.txt

    diff -ruN drupal6-print.orig/distinfo drupal6-print/distinfo
    --- drupal6-print.orig/distinfo 2008-08-02 22:58:10.000000000 +0900
    +++ drupal6-print/distinfo  2010-03-03 00:11:12.000000000 +0900
    @@ -1,3 +1,3 @@
    -MD5 (drupal/print-6.x-1.0-rc5.tar.gz) = cbf6e9cfbed672cc58fca3d9821714d1
    -SHA256 (drupal/print-6.x-1.0-rc5.tar.gz) = e2ed21a3ed96f4f41114fcab94a334cd261c1dfbe07c24faf0df15bd64fbb3b1
    -SIZE (drupal/print-6.x-1.0-rc5.tar.gz) = 71435
    +MD5 (drupal/print-6.x-1.10.tar.gz) = 93ef2b851df2cb47082fd605a309cdeb
    +SHA256 (drupal/print-6.x-1.10.tar.gz) = 74f0a54ad85519d919226c44212a49f56724dbde696ceea0d54f0539c3ffd6b8
    +SIZE (drupal/print-6.x-1.10.tar.gz) = 163040

## www/drupal6-pathauto

    diff -ruN drupal6-pathauto.orig/Makefile drupal6-pathauto/Makefile
    --- drupal6-pathauto.orig/Makefile  2010-02-05 20:44:48.000000000 +0900
    +++ drupal6-pathauto/Makefile 2010-03-03 01:00:35.000000000 +0900
    @@ -6,8 +6,7 @@
     #

     PORTNAME=  pathauto
    -DISTVERSION= 6.x-1.2
    -PORTREVISION=  1
    +DISTVERSION= 6.x-1.3
     CATEGORIES=  www

     MAINTAINER=  nick@foobar.org
    diff -ruN drupal6-pathauto.orig/distinfo drupal6-pathauto/distinfo
    --- drupal6-pathauto.orig/distinfo  2009-12-26 04:51:28.000000000 +0900
    +++ drupal6-pathauto/distinfo 2010-03-03 01:00:38.000000000 +0900
    @@ -1,3 +1,3 @@
    -MD5 (drupal/pathauto-6.x-1.2.tar.gz) = e27d8578aa485a68e9cc48d150d24dcc
    -SHA256 (drupal/pathauto-6.x-1.2.tar.gz) = a475f57b39a017c990d696c7b9e1ef9384904e0b7ae3e85497c85130f9ebc6cb
    -SIZE (drupal/pathauto-6.x-1.2.tar.gz) = 127507
    +MD5 (drupal/pathauto-6.x-1.3.tar.gz) = 9d612b248ba1de5887ee52b69cbd1ad3
    +SHA256 (drupal/pathauto-6.x-1.3.tar.gz) = e0adbc6629649b171b6f2c79a7d27b7f6317eed8db4773a3999d86dbcdb50732
    +SIZE (drupal/pathauto-6.x-1.3.tar.gz) = 138251