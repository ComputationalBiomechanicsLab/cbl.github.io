---
layout: page
title: Blog
permalink: /blog/
---
News, announcements, and articles from our group.

<div class="posts-list">
  {% for post in site.posts %}{% include post-card.html post=post %}
  {% else %}<p>No articles have been published yet.</p>{% endfor %}
</div>
