---
layout: page
title: Post Index
---

<ul>
  {% for post in site.posts %}
    <li>
      <span class="inline-post-date">{{ post.date | date_to_string }}: </span><a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>