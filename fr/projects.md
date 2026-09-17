---
layout: default
lang: fr
alt_url: /en/projects.html
title: Projets
---

<div class="timetable">

<h1>Projets</h1>

<p class="timetable__intro">Les correspondances : là où le code sort de la ligne principale.</p>

<ul class="correspondances">

<li class="project-card">
  <h2>diggo</h2>
  <ul class="project-card__tags"><li>Python</li><li>FastAPI</li><li>Playwright</li><li>Docker</li><li>PostgreSQL</li><li>LLM</li></ul>
  <p>Pipeline automatisé de veille et de candidature pour le marché IA/ML français : scraping d'offres, scoring par mots-clés et LLM, dashboard FastAPI, génération de CV et lettre de motivation en PDF.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/diggo">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>rag-lab</h2>
  <ul class="project-card__tags"><li>LangChain</li><li>LangGraph</li><li>Qdrant</li><li>FastAPI</li></ul>
  <p>RAG sur la documentation LangChain/LangGraph : retrieval hybride (Qdrant, BM25 + RRF), reranking par cross-encoder, évaluation par LLM-juge.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/rag-lab">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>Kaggle Watson</h2>
  <ul class="project-card__tags"><li>PyTorch</li><li>DeBERTa-v3</li><li>HuggingFace</li></ul>
  <p>Compétition de NLI multilingue (15 langues) : fine-tuning de DeBERTa-v3-base, entraînement en fp16, AdamW avec cosine decay.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/Kaggle_Watson">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>Restaurant Analytics</h2>
  <ul class="project-card__tags"><li>Spring Boot</li><li>Java</li><li>MongoDB</li><li>Redis</li><li>Elasticsearch</li><li>PostgreSQL</li></ul>
  <p>Backend complet avec authentification JWT, recherche floue et géospatiale (Elasticsearch), cache Redis, leaderboard. Démo en ligne sur Railway.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/restaurant-analytics">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h2>sacrebleu <span class="project-card__badge">Contribution open source mergée</span></h2>
  <ul class="project-card__tags"><li>Python</li><li>NLP</li></ul>
  <p>Correction d'une fuite d'instances dans mjpost/sacrebleu, via <code>lru_cache</code> sur <code>TokenizerSPM.__call__</code>.</p>
  <a class="project-card__link" href="https://github.com/mjpost/sacrebleu">Voir le projet &rarr;</a>
</li>

<li class="project-card">
  <h2>pyserini <span class="project-card__badge">Contribution open source mergée</span></h2>
  <ul class="project-card__tags"><li>Python</li><li>Information Retrieval</li></ul>
  <p>Correction de l'ordre des arguments de <code>getTopicsWithStringIdsFromFileWithTopicReaderClass</code> dans castorini/pyserini.</p>
  <a class="project-card__link" href="https://github.com/castorini/pyserini">Voir le projet &rarr;</a>
</li>

</ul>

</div>
