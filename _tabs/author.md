---
layout: page
icon: fa fa-user
order: 5
---

<div id="authors" class="d-flex flex-wrap mx-xl-2">
  {% assign authors = site.data.authors | sort %}
  
  {% for author in authors %}
    <div>
      <a class="tag" href="{{ author[1].url | relative_url }}">
        {{ author[1].name }}
      </a>
    </div>
  {% endfor %}
</div>
