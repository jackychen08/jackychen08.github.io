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

I am a third-year Ph.D. student in the [Joint Carnegie Mellon–University of Pittsburgh Ph.D. Program in Computational Biology](https://www.cmu.edu/compbio/), advised by [David Koes](https://bits.csb.pitt.edu/). My research focuses on machine learning methods for molecular discovery and sampling. Prior to my Ph.D., I worked with Alan Cheng at Merck on deep learning for ADMET prediction. I received my undergraduate degree from Johns Hopkins University, where I worked with [Jeffrey Gray](https://graylab.jhu.edu/) on diffusion models for protein docking.

{% if site.data.education.size > 0 or site.data.experience.size > 0 -%}
<section class="cv-grid">
  {%- if site.data.education.size > 0 %}
  <div>
    <h2>Education</h2>
    {% include cv_list.html items=site.data.education %}
  </div>
  {%- endif %}
  {%- if site.data.experience.size > 0 %}
  <div>
    <h2>Experience</h2>
    {% include cv_list.html items=site.data.experience %}
  </div>
  {%- endif %}
</section>
{%- endif %}

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
