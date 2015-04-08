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
<section>
<h4>About me</h4>
<div>
 一个测试工程师，主要从事移动端相关工作。
 <a href="/about.html">更多信息</a> 
<br/>
<br/>
<strong><font color="red"><a href="/atom.xml" target="_blank">订阅本站</a></font></strong>
<br/><br/>
联系博主：chendoing@gmail.com
</div>
</section>

