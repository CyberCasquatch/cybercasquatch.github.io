---
layout: default
title:  "Another Security Blog"
date:   2024-09-03
author: C. Casquatch
comments: false
---

# New posts should show here...


{% for post in site.posts %}
  <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p>{{ post.excerpt }}</p>
{% endfor %}

