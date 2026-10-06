---
layout: default
title: Homepage
---
<section class="profile">
  {%- if site.author.photo and site.author.photo != "" %}
  <img class="profile-photo" src="{{ site.author.photo | relative_url }}" alt="Photo of {{ site.author.name }}">
  {%- endif %}
  <div>
    <h1>{{ site.author.name }}</h1>
    {%- if site.author.position != "" or site.author.affiliation != "" %}
    <p class="role">{{ site.author.position }}{% if site.author.position != "" and site.author.affiliation != "" %}<br>{% endif %}{{ site.author.affiliation }}</p>
    {%- endif %}
    {% include links.html %}
  </div>
</section>

<!-- Bio: replace this placeholder with 1-2 paragraphs about your research. -->
Bio coming soon.

{% if site.data.news.size > 0 -%}
<h2>News</h2>
<ul class="news">
  {%- for item in site.data.news %}
  <li><span class="news-date">{{ item.date | date: "%b %Y" }}</span> {{ item.text | markdownify | remove: "<p>" | remove: "</p>" | strip }}</li>
  {%- endfor %}
</ul>
{%- endif %}

{% assign selected = site.data.publications | where: "selected", true -%}
{% if selected.size > 0 -%}
<h2>Selected Publications</h2>
<ol class="pub-list">
  {%- for pub in selected %}
  {% include publication.html pub=pub %}
  {%- endfor %}
</ol>
<p><a href="{{ '/publications' | relative_url }}">All publications &rarr;</a></p>
{%- endif %}
