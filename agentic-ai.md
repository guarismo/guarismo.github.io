---
layout: default
title: Agentic AI
permalink: /agentic-ai/
---

## 🤖 Agentic AI

Experiments, workflows, and tools built around AI agents — from autonomous coding assistants to
security automation pipelines.

---

<div class="category-section" id="claude-code">

### Claude Code

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'claude-code'" | sort: 'date' %}
{% if posts.size > 0 %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
{% else %}
<li style="border-left: none; color: rgba(255,255,255,0.4); font-style: italic;">No posts yet — coming soon.</li>
{% endif %}
</ul>
</div>

---

<div class="category-section" id="openclaw">

### OpenClaw

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'openclaw'" | sort: 'date' %}
{% if posts.size > 0 %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
{% else %}
<li style="border-left: none; color: rgba(255,255,255,0.4); font-style: italic;">No posts yet — coming soon.</li>
{% endif %}
</ul>
</div>

---

<div class="category-section" id="pentest-workflow">

### Penetration Testing Workflow

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'pentest-workflow'" | sort: 'date' %}
{% if posts.size > 0 %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
{% else %}
<li style="border-left: none; color: rgba(255,255,255,0.4); font-style: italic;">No posts yet — coming soon.</li>
{% endif %}
</ul>
</div>
