---
layout: page
icon: fa fa-user
order: 5
---
<div class="container">
  <h1>All Authors</h1>
  <ul>
    {% for author_id in site.data.authors %}
      {% assign author_info = site.data.authors[author_id[0]] %}
      <li>
        <a href="{{ author_info.url }}">{{ author_info.name }}</a>
      </li>
    {% endfor %}
  </ul>
</div>