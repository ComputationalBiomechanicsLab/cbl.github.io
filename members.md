---
layout: page
title: Our Members
permalink: /members/
---
Meet the people behind our organization.

<div class="members-grid">
  {% assign sorted_members = site.members | sort: "order" %}
  {% for member in sorted_members %}
    {% include member-card.html member=member %}
  {% else %}<p>Member profiles will be added soon.</p>{% endfor %}
</div>
