---
layout: page
permalink: /opinions/index.html
title: Research Opinions
---

<div class="opinions-header">
  <h1>Some Opinions</h1>
  <p>Mostly a scratch space for me to force the habit of reading papers. I will try to populate this with my thought on papers, ideas, topics etc. Hopefully I am more frequent than I expect myself to be!</p>
</div>

<div class="search-container">
  <input type="text" id="opinion-search" placeholder="Search opinions by title, topic, or keyword..." />
  <div class="filter-tags">
    <button class="filter-btn active" data-filter="all">All</button>
    <button class="filter-btn" data-filter="language">NLP</button>
    <button class="filter-btn" data-filter="speech">Speech</button>
    <button class="filter-btn" data-filter="multimodal">Multimodal</button>
    <button class="filter-btn" data-filter="theory">Theory</button>
    <button class="filter-btn" data-filter="random-takes">Random Takes</button>
  </div>
</div>



<div class="opinions-grid" id="opinions-container">
  {% assign sorted_opinions = site.opinions | sort: 'date' | reverse %}
  {% for opinion in sorted_opinions %}
  <article class="opinion-card" data-tags="{{ opinion.tags | join: ' ' }}" data-topic="{{ opinion.topic | downcase }}">
    <div class="opinion-meta">
      <time class="opinion-date">{{ opinion.date | date: "%b %d, %Y" }}</time>
      <span class="opinion-topic topic-{{ opinion.topic | downcase }}">{{ opinion.topic }}</span>
    </div>
    
    <h3 class="opinion-title">
      <a href="{{ site.url }}{{ opinion.url }}">{{ opinion.title }}</a>
    </h3>
    
    <div class="opinion-preview">
      {{ opinion.excerpt | strip_html | truncate: 150 }}
    </div>
    
    {% if opinion.paper_title %}
    <div class="paper-reference">
      <strong>Paper:</strong> "{{ opinion.paper_title }}"
      {% if opinion.paper_authors %}<br><em>{{ opinion.paper_authors }}</em>{% endif %}
    </div>
    {% endif %}
    
    <div class="opinion-tags">
      {% for tag in opinion.tags %}
      <span class="tag">{{ tag }}</span>
      {% endfor %}
    </div>
    
    <div class="opinion-footer">
      <a href="{{ site.url }}{{ opinion.url }}" class="read-more">Read Full Opinion →</a>
      <div class="opinion-engagement">
        <span class="reading-time">{{ opinion.content | number_of_words | divided_by: 200 | plus: 1 }} min read</span>
      </div>
    </div>
  </article>
  {% endfor %}
</div>

<style>
.opinions-header {
  text-align: center;
  margin-bottom: 2rem;
  padding: 1.5rem 0;
  border-bottom: 1px solid #333;
}

.opinions-header h1 {
  color: #333;
  margin-bottom: 0.5rem;
  font-size: 2rem;
  font-weight: normal;
}

.opinions-header p {
  color: #666;
  font-size: 1rem;
  max-width: 600px;
  margin: 0 auto;
  line-height: 1.5;
  font-style: italic;
}

.search-container {
  margin-bottom: 2rem;
  text-align: center;
}

#opinion-search {
  width: 100%;
  max-width: 500px;
  padding: 8px 12px;
  font-size: 1rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  outline: none;
}

#opinion-search:focus {
  border-color: #333;
}

.filter-tags {
  margin-top: 1rem;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.filter-btn {
  padding: 6px 12px;
  background: white;
  border: 1px solid #ccc;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.9rem;
  color: #333;
}

.filter-btn:hover, .filter-btn.active {
  background: #333;
  color: white;
  border-color: #333;
}

.opinions-grid {
  display: block;
  margin-top: 2rem;
}

.opinion-card {
  background: white;
  border: 1px solid #ddd;
  margin-bottom: 2rem;
  padding: 1.5rem;
}

.opinion-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  font-size: 0.9rem;
  color: #666;
}

.opinion-date {
  font-family: monospace;
}

.opinion-topic {
  padding: 2px 6px;
  border: 1px solid #ccc;
  font-size: 0.8rem;
  text-transform: uppercase;
  background: #f8f8f8;
}

.opinion-title {
  margin-bottom: 0.75rem;
}

.opinion-title a {
  color: #333;
  text-decoration: none;
  font-size: 1.2rem;
  font-weight: bold;
}

.opinion-title a:hover {
  text-decoration: underline;
}

.opinion-preview {
  color: #666;
  line-height: 1.6;
  margin-bottom: 1rem;
}

.paper-reference {
  background: #f8f8f8;
  padding: 0.75rem;
  margin-bottom: 1rem;
  font-size: 0.9rem;
  border-left: 2px solid #333;
}

.opinion-tags {
  margin-bottom: 1rem;
}

.tag {
  display: inline-block;
  background: #f0f0f0;
  color: #333;
  padding: 2px 6px;
  font-size: 0.8rem;
  margin-right: 0.5rem;
  margin-bottom: 0.25rem;
  border: 1px solid #ccc;
}

.opinion-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 1rem;
  border-top: 1px solid #eee;
}

.read-more {
  color: #333;
  text-decoration: none;
  font-weight: normal;
  font-size: 0.9rem;
}

.read-more:hover {
  text-decoration: underline;
}

.reading-time {
  color: #999;
  font-size: 0.8rem;
  font-family: monospace;
}

@media (max-width: 768px) {
  .opinions-grid {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }
  
  .opinions-stats {
    gap: 1.5rem;
  }
  
  .filter-tags {
    justify-content: center;
  }
}

.hidden {
  display: none;
}
</style>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const searchInput = document.getElementById('opinion-search');
  const filterBtns = document.querySelectorAll('.filter-btn');
  const opinionCards = document.querySelectorAll('.opinion-card');
  const container = document.getElementById('opinions-container');
  
  let currentFilter = 'all';
  
  // Search functionality
  searchInput.addEventListener('input', function() {
    const searchTerm = this.value.toLowerCase();
    filterAndSearch();
  });
  
  // Filter functionality
  filterBtns.forEach(btn => {
    btn.addEventListener('click', function() {
      filterBtns.forEach(b => b.classList.remove('active'));
      this.classList.add('active');
      currentFilter = this.dataset.filter;
      filterAndSearch();
    });
  });
  
  function filterAndSearch() {
    const searchTerm = searchInput.value.toLowerCase();
    let visibleCount = 0;
    
    opinionCards.forEach(card => {
      const title = card.querySelector('.opinion-title a').textContent.toLowerCase();
      const preview = card.querySelector('.opinion-preview').textContent.toLowerCase();
      const tags = card.dataset.tags.toLowerCase();
      const topic = card.dataset.topic;
      
      const matchesSearch = searchTerm === '' || 
                          title.includes(searchTerm) || 
                          preview.includes(searchTerm) || 
                          tags.includes(searchTerm);
      
      const matchesFilter = currentFilter === 'all' || 
                          topic === currentFilter || 
                          tags.includes(currentFilter);
      
      if (matchesSearch && matchesFilter) {
        card.classList.remove('hidden');
        visibleCount++;
      } else {
        card.classList.add('hidden');
      }
    });
    
    // Update stats
    // Removed stats counter as requested
  }
  
  // Animation on scroll
  const observerOptions = {
    threshold: 0.1,
    rootMargin: '0px 0px -50px 0px'
  };
  
  const observer = new IntersectionObserver(function(entries) {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        entry.target.style.opacity = '1';
        entry.target.style.transform = 'translateY(0)';
      }
    });
  }, observerOptions);
  
  opinionCards.forEach(card => {
    card.style.opacity = '0';
    card.style.transform = 'translateY(20px)';
    card.style.transition = 'opacity 0.6s ease, transform 0.6s ease';
    observer.observe(card);
  });
});
</script>