---
layout: homepage
title: Blog
---

## Blog

Notes on research, engineering, and things I am currently exploring.

<div class="blog-list">
{% assign posts_count = site.posts | size %}
{% if posts_count > 0 %}
  {% for post in site.posts %}
  <article class="blog-list-item">
    <div class="blog-meta">{{ post.date | date: "%B %-d, %Y" }}</div>
    <h3 class="blog-title"><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
    {% if post.summary %}
    <p class="blog-excerpt">{{ post.summary }}</p>
    {% else %}
    <p class="blog-excerpt">{{ post.excerpt | strip_html | truncate: 180 }}</p>
    {% endif %}
  </article>
  {% endfor %}
{% else %}
  <p>No posts yet. The first one is coming soon.</p>
{% endif %}
</div>
