---
layout: default
title: SANS Holiday Hack Challenges
permalink: /ctf/sans-hhc/
---

## 🎄 SANS Holiday Hack Challenge Writeups

Annual CTF challenges from SANS — featuring puzzles, exploitation, forensics, and holiday cheer.

---

<div class="category-section" id="hhc-2025">

### 🎄 Holiday Hack Challenge 2025

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'hhc-2025'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>

---

<div class="category-section" id="hhc-2022">

### 🎄 Holiday Hack Challenge 2022

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'hhc-2022'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>

---

<div class="category-section" id="hhc-2019">

### 🎄 Holiday Hack Challenge 2019

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'hhc-2019'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>

---

<div class="category-section" id="hhc-2016">

### 🎄 Holiday Hack Challenge 2016

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'hhc-2016'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>
