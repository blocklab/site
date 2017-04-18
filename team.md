---
layout: page
title: Team
permalink: /team/
---

### Mitglieder des blockLAB Stuttgart-Netzwerkes

<!-- The infos of each member are maintained in `_data/team.yml` -->
<ul>
{% for member in site.data.team %}
  <li>
    <a href="#{{ member.id }}">
      {% if member.title %}
        {{ member.title }}
      {% endif %}
      {{ member.name }}
    </a>
  </li>
{% endfor %}
</ul>

{% for member in site.data.team %}
  <a name="{{ member.id}}"/>
  <h3>
    {% if member.title %}
      {{ member.title }}
    {% endif %}
    {{ member.name }}
  </h3>
  <h4>{{ member.topics }}</h4>
  <img src="{{ member.image }}" alt="Portrait {{ member.name }}" class="team__portrait">
  <p>{{ member.description }}</p>
  {{ member.contact }}
  <hr>
{% endfor %}
