---
layout: default
title: Scriptoteket
---

<div id="home">

{% for post in site.posts %}
  <h2 class="post-title"><a href="{{ post.url }}">{{ post.title }}</a></h2>
  <p class="post-meta">{{ post.date | date_to_string }}</p>
  <p class="post-excerpt">{{ post.excerpt | strip_html }}&hellip; (<a href="{{ post.url }}">Read More</a>)</p>
{% endfor %}

<!--
  <h2>Reference</h2>
  <ul class="posts">
     <li>
        <a href="pages/utviklingsserver.html">Utviklingsserver</a>
     </li>
     <li>
        <a href="pages/katapi.html">Katalogdata fra Bibsys</a>
     </li>
     <li>
        <a href="pages/primo.html">Primo/Alma : hvilke muligheter har vi?</a>
     </li>
     <li>
        <a href="pages/bibduck.html">Bibduck</a>
     </li>
     <li>
        <a href="pages/propagandasenter.html">Propagandasenter</a>
     </li>
    <li>
        <a href="pages/bib-data.html">Å jobbe med bibliografiske data</a>
     </li>
    <li>
        <a href="pages/dyplenking-oria.html">Dyplenking i Oria</a>
     </li>
  </ul>
-->
<!--
  <p>
   Test1: {{ site.url }}
   Test2: {{ site.github.url }}

   Test3: {% if site.github.url == nil %}{{ site.url }}{% else %}{{ site.github.url }}{% endif %}

  </p>-->

  <!--<ul class="posts">
    {% for post in site.posts %}
      <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>-->
</div>
