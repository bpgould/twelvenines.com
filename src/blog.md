---
layout: default.html
title: Blog
---

## Blog Posts

{% for post in collections.posts.pages %}

### [{{ post.title }}]({{ post.permalink }})

{{ post.published_date | date: "%B %d, %Y" }}

{% if post.description %}
{{ post.description }}
{% endif %}

---

{% endfor %}
