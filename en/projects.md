---
layout: default
lang: en
alt_url: /fr/projects.html
title: Projects
---

<div class="timetable">

<h1>Projects</h1>

<p class="timetable__intro">The interchanges: where the code leaves the main line.</p>

<ul class="correspondances">

<li class="project-card">
  <h2>diggo</h2>
  <ul class="project-card__tags"><li>Python</li><li>FastAPI</li><li>Playwright</li><li>Docker</li><li>PostgreSQL</li><li>LLM</li></ul>
  <p>Automated job-search pipeline for the French AI/ML market: offer scraping, keyword and LLM scoring, FastAPI dashboard, CV and cover-letter PDF generation.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/diggo">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>rag-lab</h2>
  <ul class="project-card__tags"><li>LangChain</li><li>LangGraph</li><li>Qdrant</li><li>FastAPI</li></ul>
  <p>RAG over the LangChain/LangGraph docs: hybrid retrieval (Qdrant, BM25 + RRF), cross-encoder reranking, LLM-as-judge evaluation.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/rag-lab">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>Kaggle Watson</h2>
  <ul class="project-card__tags"><li>PyTorch</li><li>DeBERTa-v3</li><li>HuggingFace</li></ul>
  <p>Multilingual NLI competition (15 languages): DeBERTa-v3-base fine-tuning, fp16 training, AdamW with cosine decay.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/Kaggle_Watson">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>Restaurant Analytics</h2>
  <ul class="project-card__tags"><li>Spring Boot</li><li>Java</li><li>MongoDB</li><li>Redis</li><li>Elasticsearch</li><li>PostgreSQL</li></ul>
  <p>Full backend with JWT auth, fuzzy and geospatial search (Elasticsearch), Redis cache, leaderboard. Live demo on Railway.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/restaurant-analytics">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>sacrebleu <span class="project-card__badge">Merged open-source contribution</span></h2>
  <ul class="project-card__tags"><li>Python</li><li>NLP</li></ul>
  <p>Fixed an instance leak in mjpost/sacrebleu via <code>lru_cache</code> on <code>TokenizerSPM.__call__</code>.</p>
  <a class="project-card__link" href="https://github.com/mjpost/sacrebleu">View the project &rarr;</a>
</li>

<li class="project-card">
  <h2>pyserini <span class="project-card__badge">Merged open-source contribution</span></h2>
  <ul class="project-card__tags"><li>Python</li><li>Information Retrieval</li></ul>
  <p>Fixed the argument order for <code>getTopicsWithStringIdsFromFileWithTopicReaderClass</code> in castorini/pyserini.</p>
  <a class="project-card__link" href="https://github.com/castorini/pyserini">View the project &rarr;</a>
</li>

</ul>

</div>
