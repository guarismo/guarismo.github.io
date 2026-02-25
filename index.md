---
layout: default
title: The Internet of Stuff and Things
---

## Recent Posts

<ul class="post-list">
{% for post in site.posts limit:10 %}
{% if post.title != "" %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }} &middot; {{ post.category }}</span>
  {% if post.tags %}
  <div class="post-tags">
    {% for tag in post.tags %}
      <span>{{ tag }}</span>
    {% endfor %}
  </div>
  {% endif %}
</li>
{% endif %}
{% endfor %}
</ul>

---

## All Posts

<ul class="post-list">
{% for post in site.posts %}
{% if post.title != "" %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }} &middot; {{ post.category }}</span>
</li>
{% endif %}
{% endfor %}
</ul>
