---
title: "Python"
layout: archive
permalink: categories/py
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.PY %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
