---
layout: page
title: chenDoInG's blog on github!
tagline: My blog
---
{% include JB/setup %}

    
#My Posts

Here's is my "posts list".

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>



