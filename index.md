---
layout: page
title: 学海无涯
tagline: chenDoInG的BLOG
---
{% include JB/setup %}

    
#My Posts

Here's is my "posts list".

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>



