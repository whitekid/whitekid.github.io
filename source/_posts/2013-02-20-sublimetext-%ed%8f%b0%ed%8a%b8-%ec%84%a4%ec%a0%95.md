---
id: 1211
title: Sublimetext 설정
author: whitekid
layout: post
guid: http://blog.woosum.net/?p=1211
permalink: /archives/1211
dsq_thread_id:
  - 1094231112
categories:
  - Uncategorized
tags:
  - sublimetext
---
### 폰트 설정

Preference > Settings - User

    // Settings in here override those in "Default/Preferences.sublime-settings",  
    // and are overridden in turn by file type specific settings.  
    {  
      "font_face": "나눔고딕코딩",  
      "font_size": 16  
    }  

ref. <http://docs.sublimetext.info/en/latest/reference/settings.html>

### Command line 설정

    $ ln -s "/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl" ~/bin/subl  

### Package 설정

[Sublime Package Control][1] 설치한 후에 아래 패키지 설치치

  * Python Auto Complete
  * Django Click
  * Djanero

 [1]: http://wbond.net/sublime_packages/package_control