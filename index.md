---
layout: default
permalink: /
---

# hello!

I'm **Prabhav Singh** *(pronounced **Pruh-bhav**)*.

I’m currently pursuing my **Master’s in Computer Science (Thesis)** with a specialization in [Human Language Technologies](https://www.clsp.jhu.edu/human-language-technology-masters/) at [Johns Hopkins University](https://engineering.jhu.edu), where I conduct research at the [Center for Language and Speech Processing (CLSP)](https://www.clsp.jhu.edu/). I’m fortunate to be advised by [Prof. Jason Eisner](https://www.cs.jhu.edu/~jason/) and [Prof. Jesus Villalba](https://engineering.jhu.edu/faculty/jesus-villalba/). 

Before this, I earned my **Bachelor’s in Electrical Engineering** from [Delhi University](https://www.du.ac.in), where I worked with [Prof. K.P.S. Rana](https://sites.google.com/site/kpsrana1/home) and [Prof. Vineet Kumar](http://nsut.ac.in/en/node/554) at the APC Lab, NSIT.

You can find more details in my [CV](https://Prabhav55221.github.io/file/prabhavsresume.pdf) or read more about me [here](https://Prabhav55221.github.io/about). Feel free to reach out at: `psingh54 at jhu dot edu`

---
## Research Interests

My research interests are focused on **method development in NLP**, with an emphasis on approaches that are adaptable to supervision constraints and aligned with how **humans naturally teach, label, and reason**. I am interested in developing methods in the *fuzzy area*, where we must learn from partial feedback, conflicting signals, and implicit preferences.

<div class="research-list" markdown="1">

1. **Clarification, Uncertainty & Attribution:** Studying how *models navigate ambiguous interactions*: knowing when and how to seek clarification, expressing uncertainty and underspecification.

2. **Human-AI Collaboration:** Building frameworks that optimize decisions in human-LLM workflows, determining when human judgment is needed versus when LLMs suffice (See [this](https://Prabhav55221.github.io/marformer) and [this](https://Prabhav55221.github.io/file/MASCSLL-FINAL.pdf)).

3. **Reasoning in LLMs:** Understanding and surfacing uncertainty in LLM reasoning, and grounding reasoning chains in relevant documents from the model’s training data.

</div>

Previously, owing to my background in ECE, I have also worked a lot on speech representation and speaker systems. These days, I find my interest more in language and methoids. You can read more about my previous research below:

<details>
<summary><strong>Previous Interests</strong></summary>

<br>

<p>
I began my research journey with emotion recognition, and while I’ve developed a fair amount of expertise in that space. I was also drawn to <strong>speaker recognition</strong> and <strong>diarization</strong>. I found diarization particularly interesting — it's a fundamental speech task with open challenges in <em>temporal structure, multimodal fusion, and low-resource adaptation</em>. I also dabbled in mulimodality: Fusing audio, text, and vision to solve tasks that are natural for humans — but hard for machines. Some of my papers in this field are: 
</p>

<ul>
  <li><strong>Emotion Recognition</strong> (<a href="https://www.isca-archive.org/interspeech_2025/singh25b_interspeech.html">example</a>)</li>
  <li><strong>Speaker Diarization</strong> (<a href="https://www.isca-archive.org/interspeech_2025/singh25_interspeech.html">example</a>)</li>
  <li><strong>Speaker Recognition</strong> (<a href="https://Prabhav55221.github.io/file/2025286331.pdf">example</a>)</li>
</ul>

</details>

---
## Selected Publications

<div class="pub-list">

<div class="pub-item">
  <div class="pub-title"><a href="https://www.isca-archive.org/interspeech_2025/singh25_interspeech.html" target="_blank">Count Your Speakers! Multitask Learning for Multimodal Speaker Diarization</a></div>
  <div class="pub-authors"><strong class="me">Prabhav Singh</strong>, Jesus Villalba, Najim Dehak</div>
  <div class="pub-venue">Interspeech 2025 · Rotterdam, Netherlands</div>
  <div class="pub-links">
    <a href="https://www.isca-archive.org/interspeech_2025/singh25_interspeech.html" class="pub-link" target="_blank">Paper</a>
    <span class="pub-award">Oral · Top 20%</span>
  </div>
</div>

<div class="pub-item">
  <div class="pub-title"><a href="https://www.arxiv.org/abs/2508.04901" target="_blank">Sensitivity of Stability: Theoretical &amp; Empirical Analysis of Replicability for Adaptive Data Selection in Transfer Learning</a></div>
  <div class="pub-authors"><strong class="me">Prabhav Singh</strong>, Jessica Sorrell</div>
  <div class="pub-venue">arXiv preprint · 2025</div>
  <div class="pub-links">
    <a href="https://www.arxiv.org/abs/2508.04901" class="pub-link" target="_blank">arXiv</a>
  </div>
</div>

<div class="pub-item">
  <div class="pub-title"><a href="https://Prabhav55221.github.io/file/MASCSLL-FINAL.pdf" target="_blank">Active Learning and Feature-Acquisition with LLMs and Humans</a></div>
  <div class="pub-authors"><strong class="me">Prabhav Singh</strong>†, Haojun Shi†, Jason Eisner</div>
  <div class="pub-venue">Mid-Atlantic Student Colloquium on Speech, Language and Learning (MASC-SLL 2025)</div>
  <div class="pub-links">
    <a href="https://Prabhav55221.github.io/file/MASCSLL-FINAL.pdf" class="pub-link" target="_blank">Poster</a>
    <span class="pub-award">Best Poster Award</span>
  </div>
</div>

</div>

<p style="font-size:0.82rem; color:var(--text-muted); margin-top:0.5rem;"><a href="/publications">See all publications →</a></p>

<div class="mobile-only">

<hr>
<h2>Recent</h2>

<div class="ps-updates" style="max-height:none; overflow-y:visible; padding-right:0;">
  {% for update in site.data.updates %}
  <div class="ps-update-item">
    <div class="ps-update-date">{{ update.date }}</div>
    <div class="ps-update-text">{{ update.text }}</div>
  </div>
  {% endfor %}
</div>

</div>
