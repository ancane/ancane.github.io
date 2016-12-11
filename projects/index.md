---
layout: default
---
<div class="page-content">
  <div class="mdl-grid">

    {% for project in site.projects %}
      {% include project.html %}
    {% endfor %}

  </div>
</div>
