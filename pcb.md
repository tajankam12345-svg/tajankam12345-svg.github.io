---
layout: page
title: "Testing circuit boards without building them"
permalink: /pcb/
---

{% assign posts = site.categories.pcb | sort: "date" %}
{% for post in posts %}
{{ forloop.index }}. [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%b %-d, %Y" }}</small>
{% endfor %}
