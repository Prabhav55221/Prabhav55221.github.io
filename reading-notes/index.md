---
layout: default
permalink: /reading-notes/
title: Reading Log
---
{% include rn-gate.html %}

<div id="rn-content" style="display:none">

<h2 style="margin-top:0">Weekly Reading Log</h2>
<p style="font-size:0.88rem;color:var(--text-muted);">Two papers every week, tracked here. Notes added each Sunday.</p>

<div style="margin-top:1.5rem;">
{% assign sorted_notes = site.reading_notes | sort: 'date' | reverse %}
{% for note in sorted_notes %}
<div class="rn-entry">
  <div class="rn-meta">{{ note.date | date: "%B %d, %Y" }}</div>
  <h3><a href="{{ site.url }}{{ note.url }}">{{ note.title }}</a></h3>
  {% if note.papers %}
  <div class="rn-papers">&#128218; {{ note.papers }}</div>
  {% endif %}
  {% if note.excerpt %}
  <div class="rn-excerpt">{{ note.excerpt | strip_html | truncate: 200 }}</div>
  {% endif %}
</div>
{% endfor %}
{% if site.reading_notes.size == 0 %}
<p style="color:var(--text-muted);font-style:italic;font-size:0.88rem;">No entries yet — first check-in coming Sunday!</p>
{% endif %}
</div>

</div>

<script>
  var m = document.createElement('meta');
  m.name = 'robots'; m.content = 'noindex,nofollow';
  document.head.appendChild(m);
</script>
