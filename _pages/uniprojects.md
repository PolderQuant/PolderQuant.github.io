---
title: University Assignments
layout: collection
permalink: /uniprojects/
collection: uniprojects
entries_layout: grid
classes: wide
author_profile: true
---


A curated list of assignments completed during my Bsc. Econometrics & Data science.\
Last update: Dec 2024 - 6

{% capture written_label %}'None'{% endcapture %}

{% for collection in site.collections %}
  {% unless collection.output == false or collection.label == "posts" %}
    {% capture label %}{{ collection.label }}{% endcapture %}
    {% if label != written_label %}
      <h2 id="{{ label | slugify }}" class="archive__subtitle">{{ label }}</h2>
      {% capture written_label %}{{ label }}{% endcapture %}
    {% endif %}
  {% endunless %}
  {% for post in collection.uniprojects %}
    {% unless collection.output == false or collection.label == "posts" %}
      {% include archive-single.html %}
    {% endunless %}
  {% endfor %}
{% endfor %}