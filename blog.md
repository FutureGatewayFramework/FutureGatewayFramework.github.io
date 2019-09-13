---
layout: default
title: Blog
permalink: /blog/
priority: 5
---

Stay tuned with lates FGF news from this page, below list present last updates from the most recent:

<ul>
  {% for post in site.posts %}
    <li><h3><small>{{ post.date | date_to_string }}</small>, <a href="{{ post.url }}">{{ post.title }}</a>, <small><i>{{ post.author }}</i></small></h3>
    <p>{{ post.shdesc }}</p></li>
  {% endfor %}


