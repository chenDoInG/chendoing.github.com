---
layout: post
author: chenDoInG
title: "Jekyll Quick Start"
description: ""
category: 
tags: [Jekyll]
---

##Jekyll命令

###Create a Post

    rake post title="Hello World"

###Create a Page

####Create pages easily via rake task:
    rake page name="about.md"
####Create a nested page:
     rake page name="pages/about.md"
####Create a page with a “pretty” path:
    rake page name="pages/about"
    # this will create the file: ./pages/about/index.html

    
