---
author: emeraldonion
comments: false
date: 2017-11-05 07:00:04+00:00
layout: default
link: https://emeraldonion.org/publications/
slug: publications
title: Publications
wordpress_id: 554
---

<ul class="posts">
    {% for post in site.posts %}
        <li><span>{{ post.date | date_to_string }}</span> » <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>
    {% endfor %}
</ul>