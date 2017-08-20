---
layout: page
title: Team
permalink: /team/
---

<!-- The infos of each member are maintained in `_data/team.yml` -->
{% for member in site.data.team %}
  <a name="{{ member.id}}"/>
  <h3>
    {% if member.title %}
      {{ member.title }}
    {% endif %}
    {{ member.name }}
  </h3>
  <h4>{{ member.topics }}</h4>
  <div>
  <img src="{{ member.image }}" alt="Portrait {{ member.name }}" class="team__portrait">
  <p>{{ member.description }}</p>
  </div>
  <div>
    Kontakt: 
    {% if member.mail %}
    <a href="mailto:{{ member.mail }}">
      <i class="fa fa-envelope" aria-hidden="true"></i>
    </a>
    {% endif %}
    {% if member.web %}
    <a href="{{ member.web }}">
      <i class="fa fa-home" aria-hidden="true"></i>
    </a>
    {% endif %}
    {% if member.github %}
    <a href="https://github.com/{{ member.github }}">
      <i class="fa fa-github" aria-hidden="true"></i>
    </a>
    {% endif %}
    {% if member.twitter %}
    <a href="https://github.com/{{ member.twitter }}">
      <i class="fa fa-twitter" aria-hidden="true"></i>
    </a>
    {% endif %}
    {% if member.xing %}
    <a href="https://www.xing.com/profile/{{ member.xing }}">
      <i class="fa fa-xing" aria-hidden="true"></i>
    </a>
    {% endif %}
    {% if member.linkedin %}
    <a href="{{ member.linkedin }}">
      <i class="fa fa-linkedin" aria-hidden="true"></i>
    </a>
    {% endif %}
  </div>
  <hr>
{% endfor %}
