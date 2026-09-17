# Personal CV / Portfolio Site Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Ship a bilingual (FR/EN), 6-page static Jekyll site on GitHub Pages that presents Arnaud Thery's CV and project portfolio through a "metro map" visual direction — career as a transit line, current role glowing as "vous êtes ici" / "you are here".

**Architecture:** Jekyll (GitHub Pages' native build, zero extra CI). One shared layout + nav + footer include. Six content pages (Accueil/CV/Projets × FR/EN) as plain Markdown/HTML with front-matter-driven language toggle. One hand-written stylesheet, no JS framework, no build tool beyond Jekyll.

**Tech Stack:** Jekyll (via the `github-pages` gem), plain HTML/CSS, vanilla `<details>` for disclosure (no custom JS), Google Fonts (Archivo + Public Sans).

**Spec:** [docs/superpowers/specs/2026-09-17-personal-cv-site-design.md](../specs/2026-09-17-personal-cv-site-design.md) and [PRODUCT.md](../../../PRODUCT.md) — both travel with this plan; read both before starting.

## Global Constraints

- GitHub Pages + Jekyll only. No JS framework, no build tool beyond Jekyll, no backend, no CMS, no contact form.
- Bilingual FR/EN: both languages fully written, not machine-translated, not synced from `diggo/config/cv.yaml` (content is rewritten once for the web, per the spec's Non-goals).
- No custom domain for v1 — no `CNAME` file. Site serves at `https://st4r4x.github.io/`.
- Repo `St4r4x.github.io`, personal GitHub account, SSH host `github.com-personal`, default branch `main`.
- No em dashes (`—`, `–` used as a dash) anywhere in page copy or commit messages — use commas, periods, or rephrase.
- Palette (from the confirmed "Plan de métro" direction): `--ink:#0b1b33; --paper:#f5f3ee; --surface:#ffffff; --line:#1e4fde; --current:#ff6a13; --muted:#9aa3b2; --border:#d8d3c6`.
- Type: Archivo (display/headings, weights 500/700/900) + Public Sans (body, weights 400/500/700), both via Google Fonts.
- Git commits: English, imperative mood, conventional-commit prefix (`feat|fix|docs|chore`), max 72-char subject, no trailing period.

---

## Task 1: Jekyll scaffold and root redirect

**Files:**
- Create: `_config.yml`
- Create: `Gemfile`
- Create: `.gitignore`
- Create: `index.html`

**Interfaces:**
- Produces: a buildable Jekyll site (`_site/` on `bundle exec jekyll build`) and the root URL `/` redirecting to `/fr/`. Every later task assumes `bundle exec jekyll build` from the repo root works.

- [ ] **Step 1: Write `_config.yml`**

```yaml
title: Arnaud Thery
description: "AI/ML Engineer, CV & Projects"
url: "https://st4r4x.github.io"
markdown: kramdown
plugins: []
exclude:
  - Gemfile
  - Gemfile.lock
  - docs
  - PRODUCT.md
  - README.md
  - vendor
```

- [ ] **Step 2: Write `Gemfile`**

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
```

- [ ] **Step 3: Write `.gitignore`**

```
_site/
.jekyll-cache/
.bundle/
Gemfile.lock
vendor/
```

- [ ] **Step 4: Write the root redirect `index.html`**

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="utf-8">
  <meta http-equiv="refresh" content="0; url=/fr/">
  <link rel="canonical" href="/fr/">
  <title>Arnaud Thery</title>
</head>
<body>
  <p><a href="/fr/">Continuer en français</a> &middot; <a href="/en/">Continue in English</a></p>
</body>
</html>
```

- [ ] **Step 5: Install dependencies**

Run: `bundle install`
Expected: exits 0, creates `Gemfile.lock` (gitignored) and a local gem bundle.

- [ ] **Step 6: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/index.html`.

Run: `grep -c "url=/fr/" _site/index.html`
Expected: `1`

- [ ] **Step 7: Commit**

```bash
git add _config.yml Gemfile .gitignore index.html
git commit -m "chore: scaffold Jekyll site with root language redirect"
```

---

## Task 2: Shared layout, nav, footer, base styles, and the French home page

**Files:**
- Create: `_layouts/default.html`
- Create: `_includes/nav.html`
- Create: `_includes/footer.html`
- Create: `assets/css/style.css`
- Create: `fr/index.md`

**Interfaces:**
- Consumes: nothing from Task 1 beyond the working build.
- Produces: every page's required front matter contract (`layout: default`, `lang: fr|en`, `alt_url: <path-to-same-page-in-other-language>`, `title: <string>`), and the CSS class vocabulary later tasks reuse verbatim: `.site-nav`, `.line-switch` (+ `__current`/`__arrow`/`__other`), `.site-footer`, `.hero` (+ `__tagline`/`__summary`/`__ctas`), `.button`, `.metro-line`, `.station` (+ `--interchange`/`--current`, `summary`, `__label`/`__meta`/`__badge`/`__detail`), `.contact`.

- [ ] **Step 1: Write `_layouts/default.html`**

```html
<!DOCTYPE html>
<html lang="{{ page.lang }}">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>{{ page.title }} — Arnaud Thery</title>
  <meta name="description" content="{{ site.description }}">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Archivo:wght@500;700;900&family=Public+Sans:wght@400;500;700&display=swap" rel="stylesheet">
  <link rel="stylesheet" href="{{ '/assets/css/style.css' | relative_url }}">
</head>
<body>
  {% include nav.html %}
  <main>
    {{ content }}
  </main>
  {% include footer.html %}
</body>
</html>
```

- [ ] **Step 2: Write `_includes/nav.html`**

```html
<header class="site-nav">
  <a class="brand" href="/{{ page.lang }}/">Arnaud Thery</a>
  <nav>
    <a href="/{{ page.lang }}/cv.html">CV</a>
    <a href="/{{ page.lang }}/projects.html">{% if page.lang == "en" %}Projects{% else %}Projets{% endif %}</a>
  </nav>
  <a class="line-switch" href="{{ page.alt_url }}" aria-label="{% if page.lang == "en" %}Switch to French{% else %}Switch to English{% endif %}">
    <span class="line-switch__current">{{ page.lang | upcase }}</span>
    <span class="line-switch__arrow" aria-hidden="true">&#8646;</span>
    <span class="line-switch__other">{% if page.lang == "en" %}FR{% else %}EN{% endif %}</span>
  </a>
</header>
```

- [ ] **Step 3: Write `_includes/footer.html`**

```html
<footer class="site-footer">
  <p class="contact">
    <a href="mailto:St4r4x@gmail.com">St4r4x@gmail.com</a> &middot;
    <a href="https://linkedin.com/in/arnaud-thery-ai">LinkedIn</a> &middot;
    <a href="https://github.com/St4r4x">GitHub</a> &middot; Paris
  </p>
</footer>
```

- [ ] **Step 4: Write the base stylesheet `assets/css/style.css`**

```css
:root {
  --ink: #0b1b33;
  --paper: #f5f3ee;
  --surface: #ffffff;
  --line: #1e4fde;
  --current: #ff6a13;
  --muted: #9aa3b2;
  --border: #d8d3c6;
  --font-display: "Archivo", sans-serif;
  --font-body: "Public Sans", sans-serif;
}

* { box-sizing: border-box; }

body {
  margin: 0;
  background: var(--paper);
  color: var(--ink);
  font-family: var(--font-body);
  line-height: 1.5;
}

a { color: var(--line); }

main { max-width: 52rem; margin: 0 auto; padding: 2rem 1.5rem 4rem; }

/* Nav */
.site-nav {
  display: flex;
  align-items: center;
  gap: 1.5rem;
  padding: 1rem 1.5rem;
  border-bottom: 3px solid var(--ink);
  flex-wrap: wrap;
}
.site-nav .brand {
  font-family: var(--font-display);
  font-weight: 900;
  font-size: 1.1rem;
  color: var(--ink);
  text-decoration: none;
  margin-right: auto;
}
.site-nav nav { display: flex; gap: 1rem; }
.site-nav nav a { color: var(--ink); text-decoration: none; font-weight: 700; }
.site-nav nav a:hover { color: var(--line); }

.line-switch {
  display: inline-flex;
  align-items: center;
  gap: 0.4rem;
  border: 2px solid var(--ink);
  border-radius: 999px;
  padding: 0.3rem 0.7rem;
  font-family: var(--font-display);
  font-weight: 700;
  text-decoration: none;
  color: var(--ink);
  background: var(--surface);
}
.line-switch:hover { background: var(--line); color: var(--surface); border-color: var(--line); }
.line-switch__current { color: var(--line); }
.line-switch:hover .line-switch__current { color: var(--surface); }

/* Footer */
.site-footer {
  padding: 1.5rem;
  border-top: 3px solid var(--ink);
  font-size: 0.9rem;
  max-width: 52rem;
  margin: 0 auto;
}
.contact a { color: var(--ink); }

/* Home hero */
.hero h1 {
  font-family: var(--font-display);
  font-weight: 900;
  font-size: clamp(2.2rem, 6vw, 3.5rem);
  margin: 1.5rem 0 0.25rem;
}
.hero__tagline {
  font-family: var(--font-display);
  font-weight: 700;
  font-size: 1.25rem;
  color: var(--line);
  margin: 0 0 1.5rem;
}
.hero__summary { max-width: 38rem; margin-bottom: 2rem; }
.hero__ctas { display: flex; gap: 1rem; flex-wrap: wrap; margin-bottom: 2.5rem; }
.button {
  display: inline-block;
  font-family: var(--font-display);
  font-weight: 700;
  text-decoration: none;
  padding: 0.6rem 1.1rem;
  border: 2px solid var(--ink);
  border-radius: 0.4rem;
  color: var(--ink);
}
.button:hover { background: var(--ink); color: var(--paper); }

/* Metro line */
.metro-line {
  list-style: none;
  margin: 0 0 2.5rem;
  padding: 0;
  border-left: 6px solid var(--muted);
  margin-left: 1.5rem;
}
.station { position: relative; padding: 0 0 2rem 2rem; }
.station::before {
  content: "";
  position: absolute;
  left: -12px;
  top: 0.35rem;
  width: 18px;
  height: 18px;
  border-radius: 50%;
  background: var(--paper);
  border: 4px solid var(--muted);
}
.station--interchange::before {
  width: 24px;
  height: 24px;
  left: -15px;
  border-width: 5px;
}
.station--current::before {
  border-color: var(--current);
  background: var(--current);
  animation: pulse 2.4s ease-in-out infinite;
}
@keyframes pulse {
  0%, 100% { box-shadow: 0 0 0 4px rgba(255, 106, 19, 0.25); }
  50% { box-shadow: 0 0 0 10px rgba(255, 106, 19, 0.12); }
}
.station summary {
  cursor: pointer;
  list-style: none;
  display: flex;
  flex-wrap: wrap;
  align-items: baseline;
  gap: 0.6rem;
}
.station summary::-webkit-details-marker { display: none; }
.station__label {
  font-family: var(--font-display);
  font-weight: 900;
  font-size: 1.15rem;
}
.station--current .station__label { color: var(--current); }
.station__meta { color: var(--muted); font-size: 0.9rem; }
.station__badge {
  font-family: var(--font-display);
  font-weight: 700;
  font-size: 0.7rem;
  letter-spacing: 0.05em;
  text-transform: uppercase;
  background: var(--current);
  color: var(--surface);
  padding: 0.15rem 0.5rem;
  border-radius: 999px;
}
.station__detail {
  margin: 0.5rem 0 0;
  padding: 0.75rem 1rem;
  background: var(--surface);
  border: 2px solid var(--border);
  border-radius: 0.5rem;
  max-width: 34rem;
}
```

- [ ] **Step 5: Write the French home page `fr/index.md`**

```markdown
---
layout: default
lang: fr
alt_url: /en/
title: Accueil
---

<div class="hero">

# Arnaud Thery

<p class="hero__tagline">Ingénieur IA/ML chez Missia. Huit ans de management d'équipe avant ça, chez Fnac.</p>

<p class="hero__summary">Je construis des services IA/ML en production : API FastAPI pour l'inférence LLM et vision par ordinateur, pipelines de données, infrastructure AWS, inférence temps réel sur edge device. Avant l'IA, j'ai géré une équipe de 8 personnes pendant 8 ans. C'est ce qui me sert le plus au quotidien : prioriser sous contrainte, communiquer clairement, tenir un engagement.</p>

<div class="hero__ctas">
  <a class="button" href="/fr/cv.html">Voir le CV complet &rarr;</a>
  <a class="button" href="/fr/projects.html">Voir les projets &rarr;</a>
</div>

</div>

<ol class="metro-line">
  <li class="station station--interchange">
    <details>
      <summary>
        <span class="station__label">Fnac</span>
        <span class="station__meta">Responsable de rayon &middot; 2015&ndash;2023</span>
      </summary>
      <p class="station__detail">8 ans de management d'une équipe de 8 personnes. Communication, priorisation, pédagogie sous contrainte.</p>
    </details>
  </li>
  <li class="station">
    <details>
      <summary>
        <span class="station__label">GoodBarber</span>
        <span class="station__meta">AI Developer (stage) &middot; Avril&ndash;Juin 2023</span>
      </summary>
      <p class="station__detail">Moteur de recherche sémantique en production (Django, sentence-transformers). Livré en 3 mois.</p>
    </details>
  </li>
  <li class="station station--current">
    <details open>
      <summary>
        <span class="station__label">Missia</span>
        <span class="station__badge">Vous êtes ici</span>
        <span class="station__meta">AI/ML Engineer &middot; depuis Septembre 2023</span>
      </summary>
      <p class="station__detail">Services FastAPI en production (inférence LLM et vision), infrastructure AWS (SageMaker, ECS, Lambda), inférence edge sur Jetson Orin NX.</p>
    </details>
  </li>
</ol>
```

- [ ] **Step 6: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/fr/index.html`.

Run: `grep -c 'station--current' _site/fr/index.html`
Expected: `1`

Run: `grep -c 'href="/en/"' _site/fr/index.html`
Expected: `1` (the language toggle points at `/en/`, even though that page doesn't exist yet)

- [ ] **Step 7: Commit**

```bash
git add _layouts _includes assets fr/index.md
git commit -m "feat: add shared layout, nav, footer, and French home page"
```

---

## Task 3: English home page

**Files:**
- Create: `en/index.md`

**Interfaces:**
- Consumes: `_layouts/default.html`, `_includes/nav.html`/`footer.html`, and every CSS class from Task 2 (`.hero`, `.metro-line`, `.station`, etc.) with no changes to any of them.

- [ ] **Step 1: Write `en/index.md`**

```markdown
---
layout: default
lang: en
alt_url: /fr/
title: Home
---

<div class="hero">

# Arnaud Thery

<p class="hero__tagline">AI/ML Engineer at Missia. Before that, eight years managing a team at Fnac.</p>

<p class="hero__summary">I build AI/ML systems in production: FastAPI services for LLM and computer vision inference, data pipelines, AWS infrastructure, real-time inference on edge devices. Before AI, I managed a team of 8 people for 8 years. That's still what serves me most day to day: prioritizing under constraint, communicating clearly, following through on a commitment.</p>

<div class="hero__ctas">
  <a class="button" href="/en/cv.html">See the full CV &rarr;</a>
  <a class="button" href="/en/projects.html">See the projects &rarr;</a>
</div>

</div>

<ol class="metro-line">
  <li class="station station--interchange">
    <details>
      <summary>
        <span class="station__label">Fnac</span>
        <span class="station__meta">Department Manager &middot; 2015&ndash;2023</span>
      </summary>
      <p class="station__detail">8 years managing a team of 8 people. Communication, prioritization, teaching under pressure.</p>
    </details>
  </li>
  <li class="station">
    <details>
      <summary>
        <span class="station__label">GoodBarber</span>
        <span class="station__meta">AI Developer (Internship) &middot; April&ndash;June 2023</span>
      </summary>
      <p class="station__detail">Production semantic search engine (Django, sentence-transformers). Shipped in 3 months.</p>
    </details>
  </li>
  <li class="station station--current">
    <details open>
      <summary>
        <span class="station__label">Missia</span>
        <span class="station__badge">You are here</span>
        <span class="station__meta">AI/ML Engineer &middot; since September 2023</span>
      </summary>
      <p class="station__detail">Production FastAPI services (LLM and vision inference), AWS infrastructure (SageMaker, ECS, Lambda), edge inference on Jetson Orin NX.</p>
    </details>
  </li>
</ol>
```

- [ ] **Step 2: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/en/index.html`.

Run: `grep -c 'href="/fr/"' _site/en/index.html`
Expected: `1` (French home page now resolvable, so the round trip works both ways)

- [ ] **Step 3: Commit**

```bash
git add en/index.md
git commit -m "feat: add English home page"
```

---

## Task 4: French CV page ("Horaire")

**Files:**
- Create: `fr/cv.md`
- Modify: `assets/css/style.css`

**Interfaces:**
- Produces: `.timetable` (+ `__intro`, and a plain `h2` rule), `.timetable-row` (+ `__head`/`__period`/`__company`), `.skills-grid`, `.edu-list`, `.cert-list` class vocabulary that Task 5 (EN CV) reuses verbatim.

- [ ] **Step 1: Append the timetable styles to `assets/css/style.css`**

```css
/* CV / timetable page */
.timetable h1 {
  font-family: var(--font-display);
  font-weight: 900;
  font-size: clamp(2rem, 5vw, 2.8rem);
  margin: 1.5rem 0 0.25rem;
}
.timetable__intro { color: var(--muted); margin-bottom: 2rem; }
.timetable h2 {
  font-family: var(--font-display);
  font-weight: 900;
  font-size: 1.3rem;
  margin: 2.5rem 0 1rem;
  border-bottom: 3px solid var(--ink);
  padding-bottom: 0.4rem;
}

.timetable-row { border-top: 2px solid var(--border); padding: 1.25rem 0; }
.timetable-row:first-of-type { border-top: none; }
.timetable-row__head {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 0.5rem;
  font-family: var(--font-display);
  font-weight: 900;
}
.timetable-row__period { font-variant-numeric: tabular-nums; color: var(--line); }
.timetable-row__company { color: var(--muted); font-weight: 700; font-family: var(--font-body); }
.timetable-row ul { margin: 0.75rem 0 0; padding-left: 1.2rem; }
.timetable-row li { margin-bottom: 0.35rem; }

.skills-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(14rem, 1fr));
  gap: 1.5rem;
  margin: 1.5rem 0;
}
.skills-grid dt { font-family: var(--font-display); font-weight: 900; color: var(--line); margin-bottom: 0.4rem; }
.skills-grid dd { margin: 0 0 1rem; color: var(--ink); }

.timetable .edu-list, .timetable .cert-list {
  list-style: none;
  padding: 0;
  margin: 0;
}
.timetable .edu-list li, .timetable .cert-list li {
  border-top: 2px solid var(--border);
  padding: 0.75rem 0;
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
  gap: 0.5rem;
}
.timetable .edu-list li:first-child, .timetable .cert-list li:first-child { border-top: none; }
```

- [ ] **Step 2: Write `fr/cv.md`**

```markdown
---
layout: default
lang: fr
alt_url: /en/cv.html
title: CV
---

<div class="timetable">

# CV

<p class="timetable__intro">Le détail, arrêt par arrêt.</p>

## Expérience

<div class="timetable-row">
  <div class="timetable-row__head">
    <span>AI/ML Engineer <span class="timetable-row__company">&middot; Missia &middot; Alternance</span></span>
    <span class="timetable-row__period">Septembre 2023 &ndash; Présent</span>
  </div>
  <ul>
    <li>Services backend Python FastAPI en production : inférence LLM et modèles de vision, pipelines de données (embeddings, chunking), tests unitaires et d'intégration, CI/CD GitHub Actions</li>
    <li>Infrastructure AWS en production (SageMaker, S3, ECS Fargate, Lambda, DynamoDB), Docker multi-arch (AMD64/ARM64), suivi d'expériences MLflow</li>
    <li>Fine-tuning de Transformers et de modèles de vision (YOLOv8, VideoMAE) sur SageMaker Spot avec Optuna, PyTorch, Scikit-learn : code modulaire, reproductible, maintenable</li>
    <li>Inférence temps réel sur edge device (Jetson Orin NX, ARM64) : reconnaissance d'action et détection de chute (ElderWatch), export ONNX et optimisation TensorRT/CUDA, privacy by design (aucune vidéo ne quitte l'appareil)</li>
    <li>Développement AI-native au quotidien : Claude Code (MCP, agents, workflows spec-driven) pour la génération, le refactoring, la revue et les tests</li>
  </ul>
</div>

<div class="timetable-row">
  <div class="timetable-row__head">
    <span>AI Developer <span class="timetable-row__company">&middot; GoodBarber &middot; Stage</span></span>
    <span class="timetable-row__period">Avril &ndash; Juin 2023</span>
  </div>
  <ul>
    <li>Backend Django en production : moteur de recherche sémantique (sentence-transformers, embeddings), tests unitaires, Docker, livré en 3 mois</li>
    <li>Intégration d'un LLM dans un service Python (preuve de concept), revue de code, sécurité API</li>
  </ul>
</div>

<div class="timetable-row">
  <div class="timetable-row__head">
    <span>Responsable de rayon <span class="timetable-row__company">&middot; Fnac &middot; CDI</span></span>
    <span class="timetable-row__period">Avril 2015 &ndash; Septembre 2023</span>
  </div>
  <ul>
    <li>Management d'une équipe de 8 personnes pendant 8 ans : communication, priorisation, pédagogie sous contrainte</li>
  </ul>
</div>

## Compétences

<dl class="skills-grid">
  <dt>IA/ML</dt>
  <dd>LLMs, RAG, LangChain, LangGraph, Machine Learning, HuggingFace Transformers, PyTorch, Scikit-learn, Pandas, ONNX, TensorRT, CUDA</dd>
  <dt>Backend &amp; APIs</dt>
  <dd>Python, Java/Spring Boot, FastAPI, REST APIs, Django</dd>
  <dt>Data &amp; Bases</dt>
  <dd>PostgreSQL, MongoDB, Elasticsearch, SQLite, Qdrant, PowerBI</dd>
  <dt>Cloud &amp; DevOps</dt>
  <dd>AWS, Docker, CI/CD, GitHub Actions, MLflow, Terraform</dd>
  <dt>Finance</dt>
  <dd>Dérivés, produits exotiques, CDS, Equity</dd>
  <dt>Leadership &amp; Collaboration</dt>
  <dd>Management d'équipe (8 ans), autonomie et prise de décision, communication transverse, gestion de la pression</dd>
</dl>

## Formation

<ul class="edu-list">
  <li><span>Master of Science BIHAR (Big Data, IA) &middot; Aflokkat / ESTIA</span><span>2024&ndash;2026</span></li>
  <li><span>Expert en Informatique et Systèmes d'Information (RNCP 40573) &middot; Aflokkat / 3W Academy</span><span>2024&ndash;2026</span></li>
  <li><span>Concepteur Développeur d'Applications (RNCP 31678) &middot; Aflokkat / ESIA</span><span>2023&ndash;2024</span></li>
  <li><span>Développeur en Intelligence Artificielle (RNCP 35770) &middot; Aflokkat / ESIA</span><span>2022&ndash;2023</span></li>
</ul>

## Certifications

<ul class="cert-list">
  <li><span>Google Project Management &middot; Google Digital Academy</span><span>2025</span></li>
  <li><span>Professional Scrum Master I (PSM I) &middot; Scrum.org</span><span>2025</span></li>
  <li><span>Agile Scrum Master Workshop &middot; Udemy</span><span>2025</span></li>
  <li><span>Introduction to Docker &middot; DataCamp</span><span>2025</span></li>
  <li><span>CI/CD for Machine Learning &middot; DataCamp</span><span>2025</span></li>
  <li><span>Google Cybersecurity &middot; Google Digital Academy</span><span>2025</span></li>
  <li><span>LLM Fundamentals &middot; Hugging Face</span><span>2025</span></li>
  <li><span>MCP Fundamentals &middot; Hugging Face</span><span>2025</span></li>
</ul>

## Langues

Français (natif) &middot; Anglais (professionnel)

</div>
```

- [ ] **Step 3: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/fr/cv.html`.

Run: `grep -c "Responsable de rayon" _site/fr/cv.html`
Expected: `1`

- [ ] **Step 4: Commit**

```bash
git add assets/css/style.css fr/cv.md
git commit -m "feat: add French CV timetable page"
```

---

## Task 5: English CV page

**Files:**
- Create: `en/cv.md`

**Interfaces:**
- Consumes: `.timetable`, `.timetable-row`, `.skills-grid`, `.edu-list`, `.cert-list` from Task 4 with no CSS changes.

- [ ] **Step 1: Write `en/cv.md`**

```markdown
---
layout: default
lang: en
alt_url: /fr/cv.html
title: CV
---

<div class="timetable">

# CV

<p class="timetable__intro">The detail, stop by stop.</p>

## Experience

<div class="timetable-row">
  <div class="timetable-row__head">
    <span>AI/ML Engineer <span class="timetable-row__company">&middot; Missia &middot; Work-study</span></span>
    <span class="timetable-row__period">September 2023 &ndash; Present</span>
  </div>
  <ul>
    <li>Fine-tuned LLMs/Transformers (HuggingFace Trainer API) and computer vision models (YOLOv8, VideoMAE) on AWS SageMaker Spot, reproducible experiments tracked in MLflow</li>
    <li>Built data pipelines: annotation (FiftyOne, SQLite), embeddings, batch processing, RAG-ready</li>
    <li>Served models via FastAPI inference APIs, CI/CD with GitHub Actions, Docker multi-arch (AMD64/ARM64), GHCR</li>
    <li>Deployed real-time edge inference on Jetson Orin NX (ARM64): action recognition and fall detection, ONNX export, TensorRT/CUDA optimization, privacy by design</li>
    <li>Day-to-day AI-native development: Claude Code (MCP, agents, spec-driven workflows) for generation, refactoring, review, and tests</li>
  </ul>
</div>

<div class="timetable-row">
  <div class="timetable-row__head">
    <span>AI Developer <span class="timetable-row__company">&middot; GoodBarber &middot; Internship</span></span>
    <span class="timetable-row__period">April &ndash; June 2023</span>
  </div>
  <ul>
    <li>Built a semantic search engine (sentence-transformers, embeddings, chunking) integrated into a Django backend, exposed through a REST API</li>
    <li>Integrated an LLM into a Python backend (proof of concept), unit tests, Docker</li>
  </ul>
</div>

<div class="timetable-row">
  <div class="timetable-row__head">
    <span>Department Manager <span class="timetable-row__company">&middot; Fnac &middot; Permanent</span></span>
    <span class="timetable-row__period">April 2015 &ndash; September 2023</span>
  </div>
  <ul>
    <li>Managed a team of 8 people for 8 years: autonomy, prioritization, communication under pressure</li>
  </ul>
</div>

## Skills

<dl class="skills-grid">
  <dt>LLMs &amp; GenAI</dt>
  <dd>LLMs, RAG, LangChain, LangGraph, Qdrant, Fine-tuning, Prompt Engineering, HuggingFace Transformers, Sentence-transformers</dd>
  <dt>Backend &amp; APIs</dt>
  <dd>Python, FastAPI, Django, REST APIs, Streamlit, Pytest</dd>
  <dt>MLOps &amp; Infra</dt>
  <dd>Docker, CI/CD, GitHub Actions, MLflow, AWS SageMaker, Optuna</dd>
  <dt>Computer Vision</dt>
  <dd>YOLOv8, VideoMAE, PyTorch, OpenMMLab, RTMPose, Pandas, ONNX, TensorRT, CUDA</dd>
  <dt>Leadership &amp; Collaboration</dt>
  <dd>Team Leadership (8 yrs), Ownership and Autonomy, Cross-functional Communication, Decision-Making Under Pressure</dd>
</dl>

## Education

<ul class="edu-list">
  <li><span>MSc in AI (Big Data, AI, Digital Humanities) &middot; Aflokkat / ESIA</span><span>2024&ndash;2026</span></li>
  <li><span>Web and AI Designer Certification &middot; Aflokkat / ESIA</span><span>2023&ndash;2024</span></li>
  <li><span>AI Developer Certification &middot; Aflokkat / ESIA</span><span>2022&ndash;2023</span></li>
</ul>

## Certifications

<ul class="cert-list">
  <li><span>Google Cybersecurity &middot; Google Digital Academy</span><span>2025</span></li>
  <li><span>LLM Fundamentals &middot; Hugging Face</span><span>2025</span></li>
  <li><span>MCP Fundamentals &middot; Hugging Face</span><span>2025</span></li>
  <li><span>CI/CD for Machine Learning &middot; DataCamp</span><span>2025</span></li>
  <li><span>Introduction to Docker &middot; DataCamp</span><span>2025</span></li>
  <li><span>Google Project Management &middot; Google Digital Academy</span><span>2025</span></li>
  <li><span>Agile Scrum Master Workshop &middot; Udemy</span><span>2025</span></li>
  <li><span>Professional Scrum Master I (PSM I) &middot; Scrum.org</span><span>2025</span></li>
</ul>

## Languages

French (native) &middot; English (professional)

</div>
```

- [ ] **Step 2: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/en/cv.html`.

Run: `grep -c "Department Manager" _site/en/cv.html`
Expected: `1`

- [ ] **Step 3: Commit**

```bash
git add en/cv.md
git commit -m "feat: add English CV timetable page"
```

---

## Task 6: French Projects page ("Correspondances")

**Files:**
- Create: `fr/projects.md`
- Modify: `assets/css/style.css`

**Interfaces:**
- Produces: `.correspondances`, `.project-card` (+ `__tags`/`__link`/`__badge`) class vocabulary that Task 7 (EN Projects) reuses verbatim.

- [ ] **Step 1: Append the project-card styles to `assets/css/style.css`**

```css
/* Projects / correspondances page */
.correspondances {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(16rem, 1fr));
  gap: 1.5rem;
  list-style: none;
  padding: 0;
  margin: 2rem 0;
}
.project-card {
  border: 3px solid var(--ink);
  border-radius: 0.5rem;
  padding: 1.25rem;
  background: var(--surface);
}
.project-card h3 { font-family: var(--font-display); font-weight: 900; margin: 0 0 0.5rem; }
.project-card p { margin: 0 0 0.75rem; }
.project-card__tags { display: flex; flex-wrap: wrap; gap: 0.4rem; margin: 0.5rem 0 0.75rem; padding: 0; list-style: none; }
.project-card__tags li {
  font-size: 0.75rem;
  font-family: var(--font-display);
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.03em;
  background: var(--paper);
  border: 1px solid var(--border);
  border-radius: 999px;
  padding: 0.15rem 0.55rem;
}
.project-card__link { font-weight: 700; }
.project-card__badge {
  display: inline-block;
  font-size: 0.7rem;
  font-family: var(--font-display);
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.03em;
  color: var(--line);
  margin-left: 0.5rem;
  vertical-align: middle;
}
```

- [ ] **Step 2: Write `fr/projects.md`**

```markdown
---
layout: default
lang: fr
alt_url: /en/projects.html
title: Projets
---

<div class="timetable">

# Projets

<p class="timetable__intro">Les correspondances : là où le code sort de la ligne principale.</p>

<ul class="correspondances">

<li class="project-card">
  <h3>diggo</h3>
  <ul class="project-card__tags"><li>Python</li><li>FastAPI</li><li>Playwright</li><li>Docker</li><li>PostgreSQL</li><li>LLM</li></ul>
  <p>Pipeline automatisé de veille et de candidature pour le marché IA/ML français : scraping d'offres, scoring par mots-clés et LLM, dashboard FastAPI, génération de CV et lettre de motivation en PDF.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/diggo">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>rag-lab</h3>
  <ul class="project-card__tags"><li>LangChain</li><li>LangGraph</li><li>Qdrant</li><li>FastAPI</li></ul>
  <p>RAG sur la documentation LangChain/LangGraph : retrieval hybride (Qdrant, BM25 + RRF), reranking par cross-encoder, évaluation par LLM-juge.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/rag-lab">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>Kaggle Watson</h3>
  <ul class="project-card__tags"><li>PyTorch</li><li>DeBERTa-v3</li><li>HuggingFace</li></ul>
  <p>Compétition de NLI multilingue (15 langues) : fine-tuning de DeBERTa-v3-base, entraînement en fp16, AdamW avec cosine decay.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/Kaggle_Watson">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>Restaurant Analytics</h3>
  <ul class="project-card__tags"><li>Spring Boot</li><li>Java</li><li>MongoDB</li><li>Redis</li><li>Elasticsearch</li><li>PostgreSQL</li></ul>
  <p>Backend complet avec authentification JWT, recherche floue et géospatiale (Elasticsearch), cache Redis, leaderboard. Démo en ligne sur Railway.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/restaurant-analytics">Voir sur GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>sacrebleu <span class="project-card__badge">Contribution open source mergée</span></h3>
  <ul class="project-card__tags"><li>Python</li><li>NLP</li></ul>
  <p>Correction d'une fuite d'instances dans mjpost/sacrebleu, via <code>lru_cache</code> sur <code>TokenizerSPM.__call__</code>.</p>
  <a class="project-card__link" href="https://github.com/mjpost/sacrebleu">Voir le projet &rarr;</a>
</li>

<li class="project-card">
  <h3>pyserini <span class="project-card__badge">Contribution open source mergée</span></h3>
  <ul class="project-card__tags"><li>Python</li><li>Information Retrieval</li></ul>
  <p>Correction de l'ordre des arguments de <code>getTopicsWithStringIdsFromFileWithTopicReaderClass</code> dans castorini/pyserini.</p>
  <a class="project-card__link" href="https://github.com/castorini/pyserini">Voir le projet &rarr;</a>
</li>

</ul>

</div>
```

- [ ] **Step 3: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/fr/projects.html`.

Run: `grep -c "<h3>" _site/fr/projects.html`
Expected: `6`

Run: `grep -c 'class="project-card__badge"' _site/fr/projects.html`
Expected: `2` (sacrebleu and pyserini)

- [ ] **Step 4: Commit**

```bash
git add assets/css/style.css fr/projects.md
git commit -m "feat: add French projects correspondances page"
```

---

## Task 7: English Projects page

**Files:**
- Create: `en/projects.md`

**Interfaces:**
- Consumes: `.correspondances`, `.project-card` from Task 6 with no CSS changes.

- [ ] **Step 1: Write `en/projects.md`**

```markdown
---
layout: default
lang: en
alt_url: /fr/projects.html
title: Projects
---

<div class="timetable">

# Projects

<p class="timetable__intro">The interchanges: where the code leaves the main line.</p>

<ul class="correspondances">

<li class="project-card">
  <h3>diggo</h3>
  <ul class="project-card__tags"><li>Python</li><li>FastAPI</li><li>Playwright</li><li>Docker</li><li>PostgreSQL</li><li>LLM</li></ul>
  <p>Automated job-search pipeline for the French AI/ML market: offer scraping, keyword and LLM scoring, FastAPI dashboard, CV and cover-letter PDF generation.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/diggo">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>rag-lab</h3>
  <ul class="project-card__tags"><li>LangChain</li><li>LangGraph</li><li>Qdrant</li><li>FastAPI</li></ul>
  <p>RAG over the LangChain/LangGraph docs: hybrid retrieval (Qdrant, BM25 + RRF), cross-encoder reranking, LLM-as-judge evaluation.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/rag-lab">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>Kaggle Watson</h3>
  <ul class="project-card__tags"><li>PyTorch</li><li>DeBERTa-v3</li><li>HuggingFace</li></ul>
  <p>Multilingual NLI competition (15 languages): DeBERTa-v3-base fine-tuning, fp16 training, AdamW with cosine decay.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/Kaggle_Watson">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>Restaurant Analytics</h3>
  <ul class="project-card__tags"><li>Spring Boot</li><li>Java</li><li>MongoDB</li><li>Redis</li><li>Elasticsearch</li><li>PostgreSQL</li></ul>
  <p>Full backend with JWT auth, fuzzy and geospatial search (Elasticsearch), Redis cache, leaderboard. Live demo on Railway.</p>
  <a class="project-card__link" href="https://github.com/St4r4x/restaurant-analytics">View on GitHub &rarr;</a>
</li>

<li class="project-card">
  <h3>sacrebleu <span class="project-card__badge">Merged open-source contribution</span></h3>
  <ul class="project-card__tags"><li>Python</li><li>NLP</li></ul>
  <p>Fixed an instance leak in mjpost/sacrebleu via <code>lru_cache</code> on <code>TokenizerSPM.__call__</code>.</p>
  <a class="project-card__link" href="https://github.com/mjpost/sacrebleu">View the project &rarr;</a>
</li>

<li class="project-card">
  <h3>pyserini <span class="project-card__badge">Merged open-source contribution</span></h3>
  <ul class="project-card__tags"><li>Python</li><li>Information Retrieval</li></ul>
  <p>Fixed the argument order for <code>getTopicsWithStringIdsFromFileWithTopicReaderClass</code> in castorini/pyserini.</p>
  <a class="project-card__link" href="https://github.com/castorini/pyserini">View the project &rarr;</a>
</li>

</ul>

</div>
```

- [ ] **Step 2: Build and verify**

Run: `bundle exec jekyll build`
Expected: exits 0, creates `_site/en/projects.html`.

Run: `grep -c "<h3>" _site/en/projects.html`
Expected: `6`

- [ ] **Step 3: Commit**

```bash
git add en/projects.md
git commit -m "feat: add English projects correspondances page"
```

---

## Task 8: Responsive pass across all six pages

**Files:**
- Modify: `assets/css/style.css`

**Interfaces:**
- Consumes: every class introduced in Tasks 2, 4, and 6. No new classes produced, only media-query refinements.

- [ ] **Step 1: Append the mobile breakpoint to `assets/css/style.css`**

```css
/* Small screens */
@media (max-width: 32rem) {
  .site-nav { padding: 0.75rem 1rem; gap: 1rem; }
  main, .site-footer { padding-left: 1rem; padding-right: 1rem; }
  .hero__ctas { flex-direction: column; }
  .hero__ctas .button { text-align: center; }
  .timetable-row__head, .timetable .edu-list li, .timetable .cert-list li {
    flex-direction: column;
    align-items: flex-start;
  }
  .correspondances { grid-template-columns: 1fr; }
}
```

- [ ] **Step 2: Build**

Run: `bundle exec jekyll build`
Expected: exits 0.

- [ ] **Step 3: Serve and visually verify at mobile and desktop widths**

Run: `bundle exec jekyll serve` (leave running)

In a browser, open `http://localhost:4000/fr/`, `http://localhost:4000/fr/cv.html`, `http://localhost:4000/fr/projects.html` (and the `/en/` equivalents) at two widths: 375px (mobile) and 1280px (desktop). Confirm for each:
- The nav does not overflow or wrap awkwardly.
- The metro line's stations stay in one readable column at both widths.
- The CV timetable rows and the certification/education lists stack cleanly at 375px (period/date moves under the title, not squeezed beside it).
- The project cards form a single column at 375px and a multi-column grid at 1280px.

Stop the server (`Ctrl+C`) once confirmed.

- [ ] **Step 4: Commit**

```bash
git add assets/css/style.css
git commit -m "style: add responsive breakpoint for mobile layout"
```

---

## Task 9: Link and HTML validation

**Files:**
- Modify: `Gemfile`

**Interfaces:**
- Consumes: the fully built `_site/` from all prior tasks. Produces nothing further tasks rely on; this is a pure verification gate before deploy.

- [ ] **Step 1: Add `html-proofer` to the Gemfile's test group**

```ruby
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins

group :test do
  gem "html-proofer"
end
```

- [ ] **Step 2: Install and build**

Run: `bundle install`
Expected: exits 0.

Run: `bundle exec jekyll build`
Expected: exits 0.

- [ ] **Step 3: Run the internal link/HTML check**

Run: `bundle exec htmlproofer ./_site --disable-external`
Expected: exits 0, "HTML-Proofer finished successfully" (all internal links between the 6 pages, the language-toggle `alt_url` targets, and asset paths resolve; no broken anchors or missing files).

If it fails, fix the reported broken path in the referencing page or include, rebuild, and rerun this step until it passes.

- [ ] **Step 4: Manually verify external links once**

With `bundle exec jekyll serve` running, click through every external link on both language versions of the Projects page (6 GitHub/upstream-repo links) and the footer (LinkedIn, GitHub, mailto) once, confirming each opens the correct destination. `html-proofer` was run with `--disable-external` to avoid flaky network checks in this and future runs, so this manual pass is the only check on external URLs.

- [ ] **Step 5: Commit**

```bash
git add Gemfile
git commit -m "test: add htmlproofer for internal link and HTML validation"
```

`Gemfile.lock` stays gitignored per the Global Constraints (each machine resolves its own, avoiding GitHub Pages platform-mismatch errors), so it is not part of this commit.

---

## Task 10: Deploy to GitHub Pages

**Files:** none (repo hosting operation)

- [ ] **Step 1: Create the GitHub repository**

On github.com, signed in as the `St4r4x` personal account, create a new **public** repository named exactly `St4r4x.github.io` (this exact name is what makes GitHub treat it as a user site). Do not initialize it with a README, license, or `.gitignore` (this repo already has all three).

- [ ] **Step 2: Add the remote and push**

```bash
git remote add origin git@github.com-personal:St4r4x/St4r4x.github.io.git
git push -u origin main
```

Expected: push succeeds, `main` now tracks `origin/main`.

- [ ] **Step 3: Verify GitHub Pages is serving from `main`**

On github.com, open the repo's **Settings &rarr; Pages**. Confirm "Build and deployment" &rarr; "Source" is set to "Deploy from a branch", branch `main`, folder `/ (root)`. GitHub auto-detects this for a `<user>.github.io` repo, but confirm rather than assume. Save if it was not already set.

- [ ] **Step 4: Wait for the first deployment and verify the live site**

In the repo's **Actions** tab (or **Settings &rarr; Pages**), wait for the "pages build and deployment" workflow to finish (usually under two minutes).

Then open `https://st4r4x.github.io/` in a browser and confirm:
- It redirects to `https://st4r4x.github.io/fr/`.
- The home page shows the metro line with "Missia" glowing and marked "Vous êtes ici".
- The language toggle switches to `https://st4r4x.github.io/en/` and back.
- `https://st4r4x.github.io/fr/cv.html` and `https://st4r4x.github.io/fr/projects.html` (and their `/en/` equivalents) load without a 404.

- [ ] **Step 5: No commit for this task** (hosting configuration only; nothing left to check in).

---

## Self-review notes

- **Spec coverage:** architecture (Task 1-2), all 6 pages (Tasks 2-3, 4-5, 6-7), language toggle (Task 2, verified in Tasks 3/5/7), responsive (Task 8), deployment (Task 10), testing per spec's own "visual check + live URL check" (Tasks 8-10). No custom domain and no backend: neither appears anywhere in this plan, matching the spec's non-goals.
- **Type/class consistency:** verified `.station--current`, `.station--interchange`, `.line-switch__current/__arrow/__other`, `.timetable-row__head/__period/__company`, `.project-card__tags/__link` are introduced once and reused with identical names in every later task that consumes them.
- **No placeholders:** every page carries real, sourced content (from `diggo/config/cv.yaml` and `diggo/config/contact.yaml`), not lorem ipsum or TBDs.
- **Deliberate scope cut:** hobbies (present in `cv.yaml`) and the phone number (present in `contact.yaml`) are intentionally left off the public site: hobbies didn't earn a place in either surface's brief, and a phone number on a plain, crawlable public page is a different exposure risk than the same number on a CV PDF emailed to one recruiter at a time. Both stay on the PDF CV only. Flag if you want either added.
