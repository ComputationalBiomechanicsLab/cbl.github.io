---
layout: page
title: Projects
permalink: /projects/
---
Explore the research projects conducted by our group.

<div class="projects-grid">
  {% assign sorted_projects = site.projects | sort: "order" %}
  {% for project in sorted_projects %}
    {% include project-card.html project=project %}
  {% else %}<p>Project information will be added soon.</p>{% endfor %}
</div>
