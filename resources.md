---
layout: page
title: Resources
permalink: /resources/
---
Explore datasets, software, publications, documentation, and other materials produced or recommended by our group.

{% assign resource_groups = site.resources | group_by: "category" | sort: "name" %}
{% for group in resource_groups %}
  <section class="resource-category">
    <h2>{{ group.name | default: "Other Resources" | escape }}</h2>
    <div class="resources-list">
      {% assign sorted_resources = group.items | sort: "order" %}
      {% for resource in sorted_resources %}{% include resource-card.html resource=resource %}{% endfor %}
    </div>
  </section>
{% else %}<p>Resources will be added soon.</p>{% endfor %}
