---
layout: page
title: Blog
---

{% raw %}
{% for post in site.posts %}
  <article>
    <h2><a href="{{ post.url }}">{{ post.title }}</a></h2>
    <p>{{ post.excerpt }}</p>
    <p>
      <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
        {% if post.tags.size > 0 %}
       <span class="tags">
         Tags:
         {% for tag in post.tags %}
           <a href="/tags/{{ tag | slugify }}">{{ tag }}</a>{% unless forloop.last %},{% endunless %}
         {% endfor %}
       </span>
     {% endif %}
    </p>
  </article>
{% endfor %}
{% endraw %}