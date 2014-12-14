---
layout: page
title: Post Index
---

{% for post in site.posts %}
  <article class="post-list">
    <span class="inline-post-date">{{ post.date | date_to_string }}</span>
    <h1><a href="{{ post.url }}">{{ post.title }}</a></h1>
    <hr>
  </article>
{% endfor %}
