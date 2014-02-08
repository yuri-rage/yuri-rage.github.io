---
layout: page
title: Homebrewed
tagline: DIY, beer, geekery
---
{% include JB/setup %}

<div class="blog-index">  
  {% assign post = site.posts.first %}
  {% assign content = post.content %}
  <h2><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2>
  {{ post.content | strip_html | truncatewords:75}}<br>
  <a href="{{ post.url }}">Read more...</a>
</div>
------------------
###All posts
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
