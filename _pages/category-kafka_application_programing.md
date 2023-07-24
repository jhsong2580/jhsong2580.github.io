---
title: "아파치 카프카 애플리케이션 프로그래밍"
layout: archive
permalink: /book/_kafka-application-programing

author_profile: true
sidebar:          # (내 커스텀 변수) 블로그 목차 보기
  nav: docs   # (내 커스텀 변수) /_data/navigation.yml에 main-sidebar의 내용을 정의
---


{% assign posts = site.categories._kafka_application_programing %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}
