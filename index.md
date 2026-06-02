---
layout: default
title: Home
---

<section class="hero">
  <div class="hero-copy">
    <h1>Interview Patterns Java</h1>
    <p>
      A clean notebook for DSA patterns, Java templates, and the mental models
      that make interview problems easier to recognize.
    </p>
  </div>

  <aside class="hero-panel">
    <strong>Focus areas</strong>
    <ul>
      <li>Pattern-first explanations</li>
      <li>Java-ready templates</li>
      <li>Short review notes before interviews</li>
    </ul>
  </aside>
</section>

<section class="section">
  <div class="section-heading">
    <div>
      <h2>Notes</h2>
      <p>Start with a pattern, then add more pages as your DSA notebook grows.</p>
    </div>
  </div>

  <div class="note-grid">
    {% assign ordered_notes = site.notes | sort: "order" %}
    {% for note in ordered_notes %}
      <a class="note-card" href="{{ note.url | relative_url }}">
        <span class="tag">{{ note.section }}</span>
        <h3>{{ note.title }}</h3>
        <p>{{ note.summary | default: note.description }}</p>
        {% if note.tags %}
          <div class="tag-row">
            {% for tag in note.tags %}
              <span class="tag">{{ tag }}</span>
            {% endfor %}
          </div>
        {% endif %}
      </a>
    {% endfor %}
  </div>
</section>
