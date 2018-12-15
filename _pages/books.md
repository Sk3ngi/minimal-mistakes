---
title: "Books"
permalink: /Books/
layout: category
author_profile: true
---
{% for post in site.categories.Books %}
 <li><span>{{ post.date | date_to_string }}</span> &nbsp; <a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
