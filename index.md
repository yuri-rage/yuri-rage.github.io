---
layout: page
title: Yuri Rage
tagline: DIY, beer, geekery
---
{% include JB/setup %}

<div class="blog-index">  
  {% for post in site.posts %}
  <h2><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2>
  {{ post.content | strip_html | truncatewords:75}}<br>
  <a href="{{ post.url }}">Read more...</a><br>
  {% endfor %}
</div>
