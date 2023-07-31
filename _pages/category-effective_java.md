---
title: "이펙티브 자바"
layout: archive
permalink: /book/_effective_java

author_profile: true
sidebar:          # (내 커스텀 변수) 블로그 목차 보기
  nav: docs   # (내 커스텀 변수) /_data/navigation.yml에 main-sidebar의 내용을 정의
---


{% assign posts = site.categories._effective_java %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
