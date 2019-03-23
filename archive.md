---
layout: page
title: Archive
---

<!-- Search posts -->
{% for post in site.posts %}
  <span style="font-size:18px;"> [ {{ post.title }} ]({{ site.baseurl }}/{{ post.url }})</span>
  <small>{{ post.date | date: '%m/%d/%Y' }}</small>
{% endfor %}
