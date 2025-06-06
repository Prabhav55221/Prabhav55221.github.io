---
layout: page
permalink: /notes/index.html
title: Notes
---

# Notes

*Mostly a scratch space for me. I will try to populate this with my thought on papers, ideas, topics etc. Hopefully I am more frequent than I expect myself to be! Best read on a laptop!*

---

<div class="search-container">
  <input type="text" id="notes-search" placeholder="Keywords: NLP, Speech, Thoughts" />
</div>

<div class="notes-list" id="notes-container">
  {% assign sorted_opinions = site.notes | sort: 'date' | reverse %}
  {% for notes in sorted_opinions %}
  <div class="notes-item" data-content="{{ notes.title | downcase }} {{ notes.content | strip_html | downcase }}">
    <div class="notes-meta">
      <span class="notes-date">{{ notes.date | date: "%Y-%m-%d" }}</span>
      {% if notes.topic %}<span class="notes-topic">[{{ notes.topic }}]</span>{% endif %}
    </div>
    
    <h3><a href="{{ site.url }}{{ notes.url }}">{{ notes.title }}</a></h3>
    
    {% if notes.paper_title %}
    <div class="paper-ref">
      <strong>Paper:</strong> {{ notes.paper_title }}
      {% if notes.paper_authors %} — {{ notes.paper_authors }}{% endif %}
    </div>
    {% endif %}
    
    <div class="notes-excerpt">
      {{ notes.excerpt | strip_html | truncate: 200 }}
    </div>
  </div>
  {% endfor %}
</div>

<style>
.search-container {
  margin: 2rem 0;
}

#notes-search {
  width: 100%;
  max-width: 600px;
  padding: 10px 16px;
  font-size: 1rem;
  border: 1px solid #ddd;
  font-family: monospace;
}

.notes-list {
  margin-top: 2rem;
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
  padding: 0 2rem;
}

.notes-item {
  margin-bottom: 3rem;
  padding-bottom: 2rem;
  border-bottom: 1px solid #eee;
}

.notes-meta {
  font-family: monospace;
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 0.5rem;
}

.notes-date {
  margin-right: 1rem;
}

.notes-topic {
  color: #333;
}

.notes-item h3 {
  margin: 0.5rem 0;
  font-size: 1.2rem;
}

.notes-item h3 a {
  color: #333;
  text-decoration: none;
}

.notes-item h3 a:hover {
  text-decoration: underline;
}

.paper-ref {
  font-size: 0.95rem;
  color: #666;
  margin-bottom: 0.75rem;
  font-style: italic;
}

.notes-excerpt {
  color: #555;
  line-height: 1.5;
}

.hidden {
  display: none;
}

@media (max-width: 768px) {
  .notes-meta {
    font-size: 0.8rem;
  }
  
  .notes-item h3 {
    font-size: 1.1rem;
  }
  
  .notes-list {
    padding: 0 1rem;
  }
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const searchInput = document.getElementById('notes-search');
  const opinionItems = document.querySelectorAll('.notes-item');
  
  searchInput.addEventListener('input', function() {
    const searchTerm = this.value.toLowerCase().trim();
    
    opinionItems.forEach(item => {
      const content = item.dataset.content;
      
      if (searchTerm === '' || content.includes(searchTerm)) {
        item.classList.remove('hidden');
      } else {
        item.classList.add('hidden');
      }
    });
  });
});
</script>