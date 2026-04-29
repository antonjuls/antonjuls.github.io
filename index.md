---
layout: default
title: Home
---

# Hi, I'm Anton Skrynnik 🥷

Mobile Tech Lead and React Native / Web Developer based in the Netherlands. 15+ years in web development, 5+ in React Native.

Currently leading mobile delivery for client teams, shipping personal mobile side projects, and exploring AI and agentic workflows — applying them across both.

[GitHub](https://github.com/antonjuls) · [LinkedIn](https://www.linkedin.com/in/askrynnik) · [Email](mailto:antonjuls@gmail.com)

## Projects

<div class="project-grid">
{% for project in site.projects %}
  <a class="project-card" href="{{ project.url | relative_url }}">
    <h3>{{ project.title }}</h3>
    <p>{{ project.description }}</p>
    {% if project.tags %}
    <div class="tags">
      {% for tag in project.tags %}
      <span class="tag">{{ tag }}</span>
      {% endfor %}
    </div>
    {% endif %}
  </a>
{% endfor %}
</div>
