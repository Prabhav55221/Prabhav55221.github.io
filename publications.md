---
layout: default
permalink: /publications/
title: Publications
---

<p style="font-size:0.82rem;color:var(--text-muted);margin-bottom:1rem;">† equal contribution. See Google Scholar for full list.</p>

## Conference Papers

<div class="pub-list">
{% for paper in site.data.publications.conferences %}
<div class="pub-item">
  <div class="pub-title">
    <a href="{{ paper.links.paper | default: paper.links.arxiv }}" target="_blank">{{ paper.title }}</a>
  </div>
  <div class="pub-authors">{{ paper.authors | replace: "Prabhav Singh", '<strong class="me">Prabhav Singh</strong>' }}</div>
  <div class="pub-venue">{{ paper.venue }}</div>
  <div class="pub-links">
    {% if paper.links.paper %}<a href="{{ paper.links.paper }}" class="pub-link" target="_blank">Paper</a>{% endif %}
    {% if paper.links.arxiv %}<a href="{{ paper.links.arxiv }}" class="pub-link" target="_blank">arXiv</a>{% endif %}
    {% if paper.links.code %}<a href="{{ paper.links.code }}" class="pub-link" target="_blank">Code</a>{% endif %}
    {% if paper.links.poster %}<a href="{{ paper.links.poster }}" class="pub-link" target="_blank">Poster</a>{% endif %}
    {% if paper.links.slides %}<a href="{{ paper.links.slides }}" class="pub-link" target="_blank">Slides</a>{% endif %}
    {% if paper.award %}<span class="pub-award">{{ paper.award }}</span>{% endif %}
    {% if paper.note %}<span class="pub-note">{{ paper.note }}</span>{% endif %}
  </div>
</div>
{% endfor %}
</div>

## Journal Articles

<div class="pub-list">
{% for paper in site.data.publications.journals %}
<div class="pub-item">
  <div class="pub-title">
    <a href="{{ paper.links.paper }}" target="_blank">{{ paper.title }}</a>
  </div>
  <div class="pub-authors">{{ paper.authors | replace: "Prabhav Singh", '<strong class="me">Prabhav Singh</strong>' }}</div>
  <div class="pub-venue">{{ paper.venue }}</div>
  <div class="pub-links">
    <a href="{{ paper.links.paper }}" class="pub-link" target="_blank">Paper</a>
    {% if paper.award %}<span class="pub-award">{{ paper.award }}</span>{% endif %}
    {% if paper.note %}<span class="pub-note">{{ paper.note }}</span>{% endif %}
  </div>
</div>
{% endfor %}
</div>

## Preprints, Workshops & Posters

<div class="pub-list">
{% for paper in site.data.publications.preprints %}
<div class="pub-item">
  <div class="pub-title">
    {% assign href = paper.links.paper | default: paper.links.arxiv | default: paper.links.poster %}
    <a href="{{ href }}" target="_blank">{{ paper.title }}</a>
  </div>
  <div class="pub-authors">{{ paper.authors | replace: "Prabhav Singh", '<strong class="me">Prabhav Singh</strong>' }}</div>
  <div class="pub-venue">{{ paper.venue }}</div>
  <div class="pub-links">
    {% if paper.links.paper %}<a href="{{ paper.links.paper }}" class="pub-link" target="_blank">Paper</a>{% endif %}
    {% if paper.links.arxiv %}<a href="{{ paper.links.arxiv }}" class="pub-link" target="_blank">arXiv</a>{% endif %}
    {% if paper.links.poster %}<a href="{{ paper.links.poster }}" class="pub-link" target="_blank">Poster</a>{% endif %}
    {% if paper.award %}<span class="pub-award">{{ paper.award }}</span>{% endif %}
    {% if paper.note %}<span class="pub-note">{{ paper.note }}</span>{% endif %}
  </div>
</div>
{% endfor %}
</div>
