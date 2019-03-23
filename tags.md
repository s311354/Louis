---
layout: page
title: Categories
---


<div id="tags">
  <ul class="tags">
  <div class="tags-expo-list">
    <strong> Tags:</strong>
    {% for tag in site.tags %}
    <a href="#{{ tag[0] | slugify }}" class="post-tag">{{ tag[0] }} <span> ({{ tag[1].size }})</span></a>
    {% endfor %}
  </div>
  <hr/>
    	{% for tag in site.tags %}
			<div>
                <h1 id="{{ tag[0] | slugify }}"><span> {{ tag | first }} </span></h1>
                <ul class="tag-list">
                        {% for post in tag[1] %}
                    <li>
                            <a class="post-title" href="{{ site.baseurl }}{{ post.url }}">
                                {{ post.title }} 
                                <small>{{ post.date | date: '%m/%d/%Y' }}</small>
                            </a>
                    </li>
                        {% endfor %}
                </ul>
			</div>
			{% endfor %}

  </ul>
</div>
