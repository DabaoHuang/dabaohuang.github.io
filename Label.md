---
layout: page
title: Label
---

<!-- category start -->
{% for category in site.categories %}
<div class="LabelBorder"><span>{{ category | first }}</span>
    <ul>
    {% for posts in category %}
      {% for post in posts %}
        {% if post.url %}<li><a href="{{ post.url }}">{{ post.title }}</a></li>{% endif %}
      {% endfor %}
    {% endfor %}
  </ul>
</div>
{% endfor %}
<!-- category end -->