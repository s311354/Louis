---
layout: page
title: Archive
---

<!-- Search posts -->
{% for post in site.posts %}
  * {{ post.date | date_to_string }} &raquo;
  <span style="font-size:18px;"> [ {{ post.title }} ]({{ site.baseurl }}/{{ post.url }})</span>
{% endfor %}
