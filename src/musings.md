---
layout: default.html
title: Musings
---

## Musings

{% for post in collections.posts.pages %}
{% if post.categories contains "musings" or post.permalink contains "musings/" %}

### [{{ post.title }}]({{ post.permalink }})

{{ post.published_date | date: "%B %d, %Y" }}

{% if post.description %}
{{ post.description }}
{% endif %}

---

{% endif %}
{% endfor %}
