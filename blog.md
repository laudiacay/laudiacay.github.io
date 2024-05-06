---
layout: page
title: Blog
---

{% for post in site.posts %}
## [{{ post.title }}]({{ post.url }})

{{ post.excerpt }}

<time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
{% if post.tags.size > 0 %}
Tags: {% for tag in post.tags %}[{{ tag }}](/tags/{{ tag | slugify }}){% unless forloop.last %}, {% endunless %}{% endfor %}
{% endif %}

{% endfor %}