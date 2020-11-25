---
layout: post
author: chenDoInG
title: "Jekyll Quick Start"
description: ""
category: 
tags: [Jekyll]
---

## Jekyll命令

### Create a Post

```javascript
rake post title="Hello World"
```

### Create a Page

#### Create pages easily via rake task:
 ```javascript
rake page name="about.md"
 ```



#### Create a nested page:
     ```javascript
rake page name="pages/about.md"
     ```



#### Create a page with a “pretty” path:
   ```javascript
rake page name="pages/about"
   ```

### this will create the file: ./pages/about/index.html

​    
