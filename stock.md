---
layout: page
title: "An AI that helps you invest"
permalink: /stock/
---

{% assign posts = site.categories.stock | sort: "date" %}
{% for post in posts %}
{{ forloop.index }}. [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%b %-d, %Y" }}</small>
{% endfor %}
