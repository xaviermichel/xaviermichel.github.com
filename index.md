---
layout: page
title: Xavier MICHEL
tagline: Mon portfolio : mes réalisations et plus !
---
{% include JB/setup %}

Bonjour et bienvenue :)

Ce site contient une liste d'articles sur mes découvertes et projets personnels.

Bonne exploration !

***

Liste des articles :

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
