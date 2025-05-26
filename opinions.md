---
layout: page
permalink: /opinions/index.html
title: Research Opinions
---

<div class="opinions-header">
  <h1>Research Opinions</h1>
  <p>Daily thoughts on papers, ideas, and the state of NLP/Speech research. A living collection of hot takes, cold analyses, and everything in between.</p>
</div>

<div class="search-container">
  <input type="text" id="opinion-search" placeholder="Search opinions by title, topic, or keyword..." />
  <div class="filter-tags">
    <button class="filter-btn active" data-filter="all">All</button>
    <button class="filter-btn" data-filter="nlp">NLP</button>
    <button class="filter-btn" data-filter="speech">Speech</button>
    <button class="filter-btn" data-filter="multimodal">Multimodal</button>
    <button class="filter-btn" data-filter="theory">Theory</button>
    <button class="filter-btn" data-filter="hot-take">Hot Takes</button>
  </div>
</div>

<div class="opinions-stats">
  <div class="stat">
    <span class="stat-number" id="total-opinions">{{ site.opinions.size }}</span>
    <span class="stat-label">Total Opinions</span>
  </div>
  <div class="stat">
    <span class="stat-number">{{ site.opinions | group_by: 'topic' | size }}</span>
    <span class="stat-label">Topics Covered</span>
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
  padding: 2rem 0;
  border-bottom: 2px solid #db3e75;
}

.opinions-header h1 {
  color: #db3e75;
  margin-bottom: 0.5rem;
  font-size: 2.5rem;
}

.opinions-header p {
  color: #666;
  font-size: 1.1rem;
  max-width: 600px;
  margin: 0 auto;
  line-height: 1.6;
}

.search-container {
  margin-bottom: 2rem;
  text-align: center;
}

#opinion-search {
  width: 100%;
  max-width: 500px;
  padding: 12px 20px;
  font-size: 1rem;
  border: 2px solid #ddd;
  border-radius: 25px;
  outline: none;
  transition: border-color 0.3s ease;
}

#opinion-search:focus {
  border-color: #db3e75;
}

.filter-tags {
  margin-top: 1rem;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
  gap: 0.5rem;
}

.filter-btn {
  padding: 8px 16px;
  background: #f8f9fa;
  border: 1px solid #ddd;
  border-radius: 20px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-size: 0.9rem;
}

.filter-btn:hover, .filter-btn.active {
  background: #db3e75;
  color: white;
  border-color: #db3e75;
}

.opinions-stats {
  display: flex;
  justify-content: center;
  gap: 3rem;
  margin-bottom: 3rem;
  padding: 1.5rem;
  background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
  border-radius: 10px;
}

.stat {
  text-align: center;
}

.stat-number {
  display: block;
  font-size: 2rem;
  font-weight: bold;
  color: #db3e75;
}

.stat-label {
  font-size: 0.9rem;
  color: #666;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.opinions-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
  gap: 2rem;
  margin-top: 2rem;
}

.opinion-card {
  background: white;
  border-radius: 12px;
  padding: 1.5rem;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
  border-left: 4px solid #db3e75;
}

.opinion-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 8px 25px rgba(0, 0, 0, 0.15);
}

.opinion-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

.opinion-date {
  color: #666;
  font-size: 0.9rem;
}

.opinion-topic {
  padding: 4px 8px;
  border-radius: 12px;
  font-size: 0.8rem;
  font-weight: bold;
  text-transform: uppercase;
}

.topic-nlp { background: #e3f2fd; color: #1976d2; }
.topic-speech { background: #f3e5f5; color: #7b1fa2; }
.topic-multimodal { background: #e8f5e8; color: #388e3c; }
.topic-theory { background: #fff3e0; color: #f57c00; }
.topic-hot-take { background: #ffebee; color: #d32f2f; }

.opinion-title {
  margin-bottom: 0.75rem;
}

.opinion-title a {
  color: #333;
  text-decoration: none;
  font-size: 1.25rem;
  font-weight: 600;
  line-height: 1.3;
}

.opinion-title a:hover {
  color: #db3e75;
}

.opinion-preview {
  color: #666;
  line-height: 1.5;
  margin-bottom: 1rem;
}

.paper-reference {
  background: #f8f9fa;
  padding: 0.75rem;
  border-radius: 6px;
  margin-bottom: 1rem;
  font-size: 0.9rem;
  border-left: 3px solid #db3e75;
}

.opinion-tags {
  margin-bottom: 1rem;
}

.tag {
  display: inline-block;
  background: #e9ecef;
  color: #495057;
  padding: 3px 8px;
  border-radius: 12px;
  font-size: 0.8rem;
  margin-right: 0.5rem;
  margin-bottom: 0.25rem;
}

.opinion-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 1rem;
  border-top: 1px solid #eee;
}

.read-more {
  color: #db3e75;
  text-decoration: none;
  font-weight: 500;
  font-size: 0.9rem;
}

.read-more:hover {
  text-decoration: underline;
}

.reading-time {
  color: #999;
  font-size: 0.8rem;
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
    document.getElementById('total-opinions').textContent = visibleCount;
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