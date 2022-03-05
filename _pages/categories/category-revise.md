---
title: "수정이 필요한 게시글 모음"
layout: archive
permalink: categories/revise
author_profile: true
sidebar_main: true
---

{% assign posts = site.categories.Revise %}
{% for post in posts %} {% include archive-single2.html type=page.entries_layout %} {% endfor %}
