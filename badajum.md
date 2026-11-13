---
layout: page
title: "Badajum: an AI that answers the phone"
permalink: /badajum/
---

{% assign posts = site.categories.badajum | sort: "date" %}
{% for post in posts %}
{{ forloop.index }}. [{{ post.title }}]({{ post.url | relative_url }}) <small>{{ post.date | date: "%b %-d, %Y" }}</small>
{% endfor %}
