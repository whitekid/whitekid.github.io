---
id: 26
title: drupal apache vhost setup
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=26
permalink: /archives/26
dsq_thread_id:
  - 1180241805
tags:
  - drupal
---
    <VirtualHost *:80>
        DocumentRoot "/usr/local/www/drupal6"
        ServerName www.woosum.net
        ServerAlias woosum.net
        <Directory "/usr/local/www/drupal6">
            Options Indexes FollowSymlinks MultiViews
            AllowOverride None
            Order allow,deny
            Allow from all

            RewriteEngine on
            RewriteBase /
            RewriteCond %{REQUEST_FILENAME} !-f
            RewriteCond %{REQUEST_FILENAME} !-d
            RewriteRule ^(.*)$ index.php?q=$1 [L,QSA]
        </Directory>
    </VirtualHost>
