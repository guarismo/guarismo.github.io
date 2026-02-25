---
layout: default
title: Miscellaneous
permalink: /misc/
---

## 📝 Miscellaneous Posts

Other security research, tools, and experiments.

---

<ul class="post-list">
{% assign posts = site.posts | where: "category", "misc" | sort: 'date' | reverse %}
{% for post in posts %}
{% if post.title != "" %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
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
