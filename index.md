---
layout: page
title: goodogod it.
tagline: 試試一個普通人可以走多遠
---
{% include JB/setup %}

文章列表：

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>