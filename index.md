---
layout: page
title: Home
hide_title: true
---
Computational Biomechanics Lab
==============================

We explore fundamental challenges in computational science, data analysis, and advanced engineering systems. Our group is dedicated to open science, collaborative mentorship, and impactful publications.

---

## Featured Projects
{% assign featured_projects = site.projects | where: "featured", true | sort: "order" %}
{% if featured_projects.size == 0 %}{% assign featured_projects = site.projects | sort: "order" %}{% endif %}
<div class="projects-list">
  {% for project in featured_projects limit:3 %}{% include project-card.html project=project %}
  {% else %}<p>Our research projects will be presented here soon. Visit the <a href="{{ '/projects/' | relative_url }}">projects page</a>.</p>{% endfor %}
</div>
<p><a href="{{ '/projects/' | relative_url }}">All projects →</a></p>

---

## Laboratory Members
<div class="members-grid">
  {% assign sorted_members = site.members | sort: "order" %}
  {% for member in sorted_members limit:6 %}{% include member-card.html member=member %}
  {% else %}<p>Our team profiles will be added soon.</p>{% endfor %}
</div>
<p><a href="{{ '/members/' | relative_url }}">Meet all members →</a></p>

---

## Latest News
<div class="posts-list">
  {% for post in site.posts limit:3 %}{% include post-card.html post=post %}
  {% else %}<p>News and articles will appear here.</p>{% endfor %}
</div>
<p><a href="{{ '/blog/' | relative_url }}">All news and articles →</a></p>

---

Explore our <a href="{{ '/resources/' | relative_url }}">resources</a> or learn <a href="{{ '/about/' | relative_url }}">more about the group</a>.
