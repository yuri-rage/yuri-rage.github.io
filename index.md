---
layout: page
title: Homebrewed 
tagline: DIY, beer, geekery
---
{% include JB/setup %}

<div class="blog-index">  
  {% for post in site.posts %}
  <h2 style="display: inline"><a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></h2><br>
  <em>{{ post.date | date: '%b %d, %Y' }}</em><br>
  {{ post.content | strip_html | truncatewords:75 }}<br>
  <a href="{{ post.url }}">Read more...</a><br><br>
  {% endfor %}
</div>
