---
layout: page 
title: Blog
pagination: 
  enabled: true
---

<hr class="double">
{% for post in paginator.posts %}
<div class="post-box">
  <h2><a href={{post.url}}>{{ post.title }}</a></h2>
  <div class="post-excerpt">
    {{ post.excerpt }}
  </div>
  <div class="post-meta">
    <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%B %d, %Y" }}</time>
    {% if post.tags.size > 0 %}
    <div class="post-tags">
      Tags: {% for tag in post.tags %}<a href=/tags/{{ tag | slugify }}>{{ tag }}</a>{% unless forloop.last %}, {% endunless %}{% endfor %}
    </div>
    {% endif %}
  </div>
</div>
<hr class="dotted">
{% endfor %}

{% if paginator.total_pages > 1 %}
<ul>
  {% if paginator.previous_page %}
  <li>
    <a href="{{ paginator.previous_page_path | prepend: site.baseurl }}">Newer</a>
  </li>
  {% endif %}
  {% if paginator.next_page %}
  <li>
    <a href="{{ paginator.next_page_path | prepend: site.baseurl }}">Older</a>
  </li>
  {% endif %}
</ul>
{% endif %}