---
title: "Clean Code _ Robert C. Martin 🖍"
layout: archive
permalink: categories/clean_code
author_profile: true
sidebar_main: true
---

***

{% assign posts = site.categories.clean_code %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}