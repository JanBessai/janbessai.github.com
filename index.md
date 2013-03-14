---
layout: page
title: λemonad
tagline: false
---
{% include JB/setup %}

## Welcome 

### About
My name is Jan Bessai. I'm currently studying computer science at
[tu-dortmund](http://www.tu-dortmund.de). This blog will be about
(functional) programming, types, category theory and some 
philosophy maybe.

### Posts
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>





