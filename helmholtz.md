---
layout: page
title: "Helmholtz: a keyboard down to the microsecond"
permalink: /helmholtz/
---

{% assign posts = site.categories.helmholtz | sort: "date" %}
{% for post in posts %}
{{ forloop.index }}. [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%b %-d, %Y" }}</small>
{% endfor %}
