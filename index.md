---
layout: default
---


{% include docs.md %}


---

# <a name="tools"/></a> Tools

<ul>
{% for tool in site.data.tools %}
  <li><a href="{{ tool.url }}">{{ tool.name }}</a></li>
{% endfor %}
</ul>

---

# <a name="hosts"/></a> /etc/hosts

```
52.16.215.68    nexus.codepot
52.19.100.152   rundeck.codepot
52.19.100.44    graphite.codepot
52.19.100.44    grafana.codepot
52.19.100.44    seyren.codepot
52.19.107.141   loggers.codepot
52.19.107.141   kibana.codepot
52.19.16.87     apps.codepot 
52.19.96.168    jenkins.codepot
```

---

# <a name="ports"/></a> Microservices addresses

<table class='table'>
  <tbody>
  {% for project in site.data.projects %}
    <tr>
      <td><a href="http://apps.{{ site.domain }}:{{ project.port }}">{{ project.name }} (port {{ project.port }})</a></td>
      <td><a href="https://github.com/Codepot-Microservices-2015-08/{{ project.name }}"> Github URL</a></td>
      <td><a href="http://apps.{{ site.domain }}:{{ project.port }}/swagger/index.html">Swagger</a></td>
      <td><a href="http://jenkins.{{ site.domain }}/job/{{ project.name }}/">Jenkins</a></td>
    </tr>
  {% endfor %}
  </tbody>
</table>

