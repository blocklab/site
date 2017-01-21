---
layout: page
title: Vergangene Meetups 
permalink: /past-meetups/
---

{% for meetup in site.data.past-meetups %}
  * <a href="{{ meetup.link}}">#{{ meetup.id }}</a>
  {% for talk in meetup.talks %}
    * __{{ talk.title }}__ - _{{ talk.speaker }}_ {% if talk.slides %}- <a href="{{ talk.slides }}">Slides</a>{% endif %}
  {% endfor %}
{% endfor %}
