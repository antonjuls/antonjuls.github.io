---
layout: default
title: Home
---

# Hi, I'm Anton Skrynnik 🥷

Mobile Tech Lead and React Native / Web Developer based in the Netherlands. 15+ years in web development, 5+ in React Native.

I'm drawn to clean code, thoughtful design, and elegant solutions — systems built on simplicity, where every decision has a reason and every line earns its place. Perfection is a horizon, not a destination — the craft lives in moving toward it, in the balance between ideal and shipped.

Currently leading mobile delivery for client teams, shipping personal mobile side projects, and exploring AI and agentic workflows — applying them across both.

[GitHub](https://github.com/antonjuls) · [LinkedIn](https://www.linkedin.com/in/askrynnik) · [Email](mailto:antonjuls@gmail.com)

## Projects

<div class="project-grid">
{% for project in site.projects %}
  <a class="project-card" href="{{ project.url | relative_url }}">
    <h3>{{ project.title }}</h3>
    {% if project.role %}<p class="project-role">{{ project.role }}</p>{% endif %}
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
