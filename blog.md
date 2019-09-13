---
layout: default
title: Blog
permalink: /blog/
order: 5
---

Stay tuned with latest FGF news from this page, below list present last updates from the most recent:

<ul>
  {% for post in site.posts %}
    <li>
      <h3>
        <font size="2"><b>{{ post.date | date_to_string }}</b></font>, <a href="{{ post.url }}">{{ post.title }}</a>, <font size="1"><i>{{ post.author }}</i>
        </font>
      </h3>
    <p style="margin-left:5%; margin-right:5%;">
      <table bgcolor="{{ post.bgcolor }}"><tr><td><small>{{ post.shdesc }}</small></td></tr>
      </table>
    </p>
    </li>
  {% endfor %}


