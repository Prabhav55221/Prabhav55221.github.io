---
layout: page
permalink: /notes/index.html
title: Notes
---

# Some Opinions

*Mostly a scratch space for me. I will try to populate this with my thought on papers, ideas, topics etc. Hopefully I am more frequent than I expect myself to be! Best read on a laptop!*

---

<div class="search-container">
  <input type="text" id="opinion-search" placeholder="Keywords: NLP, Speech, Thoughts" />
</div>

<div class="opinions-list" id="opinions-container">
  {% assign sorted_opinions = site.opinions | sort: 'date' | reverse %}
  {% for opinion in sorted_opinions %}
  <div class="opinion-item" data-content="{{ opinion.title | downcase }} {{ opinion.content | strip_html | downcase }}">
    <div class="opinion-meta">
      <span class="opinion-date">{{ opinion.date | date: "%Y-%m-%d" }}</span>
      {% if opinion.topic %}<span class="opinion-topic">[{{ opinion.topic }}]</span>{% endif %}
    </div>
    
    <h3><a href="{{ site.url }}{{ opinion.url }}">{{ opinion.title }}</a></h3>
    
    {% if opinion.paper_title %}
    <div class="paper-ref">
      <strong>Paper:</strong> {{ opinion.paper_title }}
      {% if opinion.paper_authors %} — {{ opinion.paper_authors }}{% endif %}
    </div>
    {% endif %}
    
    <div class="opinion-excerpt">
      {{ opinion.excerpt | strip_html | truncate: 200 }}
    </div>
  </div>
  {% endfor %}
</div>

<style>
.search-container {
  margin: 2rem 0;
}

#opinion-search {
  width: 100%;
  max-width: 600px;
  padding: 10px 16px;
  font-size: 1rem;
  border: 1px solid #ddd;
  font-family: monospace;
}

.opinions-list {
  margin-top: 2rem;
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
  padding: 0 2rem;
}

.opinion-item {
  margin-bottom: 3rem;
  padding-bottom: 2rem;
  border-bottom: 1px solid #eee;
}

.opinion-meta {
  font-family: monospace;
  font-size: 0.9rem;
  color: #666;
  margin-bottom: 0.5rem;
}

.opinion-date {
  margin-right: 1rem;
}

.opinion-topic {
  color: #333;
}

.opinion-item h3 {
  margin: 0.5rem 0;
  font-size: 1.2rem;
}

.opinion-item h3 a {
  color: #333;
  text-decoration: none;
}

.opinion-item h3 a:hover {
  text-decoration: underline;
}

.paper-ref {
  font-size: 0.95rem;
  color: #666;
  margin-bottom: 0.75rem;
  font-style: italic;
}

.opinion-excerpt {
  color: #555;
  line-height: 1.5;
}

.hidden {
  display: none;
}

@media (max-width: 768px) {
  .opinion-meta {
    font-size: 0.8rem;
  }
  
  .opinion-item h3 {
    font-size: 1.1rem;
  }
  
  .opinions-list {
    padding: 0 1rem;
  }
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const searchInput = document.getElementById('opinion-search');
  const opinionItems = document.querySelectorAll('.opinion-item');
  
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