---
layout: default
title: Metasploitable 3
permalink: /ctf/metasploitable/
---

## 💀 Metasploitable 3 Writeups

Comprehensive walkthroughs of the Rapid7 Metasploitable 3 CTF — a vulnerable Windows VM with card-themed flags and various exploitation paths.

---

<div class="category-section" id="cards">

### 🃏 Card Challenges

Finding the hidden flags — each card is a different vulnerability to exploit.

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'cards'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>

---

<div class="category-section" id="exploitation">

### 💣 Exploitation Guides

Deep dives into specific services and exploitation techniques.

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'exploitation'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>

---

<div class="category-section" id="vulnerability-scanning">

### 🔍 Vulnerability Scanning

Scanning and assessment of the Metasploitable 3 environment.

<ul class="post-list">
{% assign posts = site.posts | where_exp: "post", "post.tags contains 'vulnerability-scanning'" | sort: 'date' %}
{% for post in posts %}
<li>
  <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
  <span class="post-date">{{ post.date | date: "%B %d, %Y" }}</span>
</li>
{% endfor %}
</ul>
</div>
