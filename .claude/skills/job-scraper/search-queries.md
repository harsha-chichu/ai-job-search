# Search Queries for Job Scraper

<!-- Populated by /setup on 2026-11-24 for Harshavardhan Sunnam -->

## Installed portal CLIs (primary for `/scrape`)

`/scrape` discovers every portal skill under `.agents/skills/*/SKILL.md` and runs its CLI first. None ship with AI Job Search: copy the ones for your market from [community-portals](https://github.com/ai-job-search-dev/community-portals) (e.g. `linkedin-search`, `freehire-search` under `portals/global/`), or build one with `/add-portal`. You do **not** need a matching `site:` line below for those CLIs to run.

The `site:` query templates in this file are the **WebSearch fallback** — for portals without a CLI, company career pages, or when a CLI fails.

**Language scope:** write every query category in every language listed in your CLAUDE.md Languages table (typically 1-2, sometimes more). A posting requiring a language you have *not* declared, as a job condition, is excluded before scoring; a posting requiring a *higher level* than you declared in a language you *do* work in is flagged for your own judgment, not excluded — see `04-job-evaluation.md`'s Language Gate, the single source of truth for this rule. Translate each category's keywords rather than machine-translating word-for-word (e.g. "Frontend Developer" -> "Desarrollador Frontend", not a literal word-for-word translation) if you work in more than one language.

## Search Sites

Primary (Indian + remote AI job boards):
- **linkedin.com/jobs** - filter: Remote (India) first, then Bangalore hybrid/on-site; also covered by the community `linkedin-search` CLI once installed
- **naukri.com** - India's largest general job board
- **wellfound.com** - startups, many remote AI roles
- **instahyre.com** - curated tech roles, India
- **cutshort.io** / **hirist.tech** - tech-focused Indian boards (optional)

Secondary (remote-first boards and company career pages via Google):
- **remoteok.com**, **weworkremotely.com**, **remotive.com** - global remote (check India eligibility)
- Direct Google searches with `site:` filters for known target companies

## Query Categories

Queries are grouped by priority. Only English is declared, so all queries are in English. Remote is the top filter: prefer remote queries first, then Bangalore.

Each priority has two kinds of block. **Plain terms** blocks are what the portal CLIs consume: one term per line, one CLI call per term, no `OR`, no `site:` prefix, and no location (the CLI's location flag, or the city in `--query` where that portal's SKILL.md says so, carries it). Quote a multi-word phrase only if the portal's `Query syntax:` line says quoting is honoured. **WebSearch-only** blocks are Google syntax (`site:`, quotes, `OR`) for the fallback path and are never passed to a CLI.

**Organize by function, not job title.** The same underlying work carries different titles across companies and markets. Each priority category is named after the function it covers.

### Priority 1: Building production LLM / agentic AI systems

Strongest and most desired direction.

#### Plain terms (portal CLIs)

```
AI Engineer
GenAI Engineer
LLM Engineer
Agentic AI Engineer
Generative AI Developer
LangGraph
```

#### WebSearch-only (`site:` fallback)

```
site:linkedin.com/jobs "AI Engineer" remote India
site:linkedin.com/jobs "GenAI Engineer" OR "Generative AI Engineer" remote India
site:naukri.com "LLM Engineer" OR "GenAI Engineer" remote
site:wellfound.com "AI Engineer" remote India
site:instahyre.com "Generative AI" Bangalore
site:linkedin.com/jobs "Agentic AI" OR "LangGraph" Bangalore
```

### Priority 2: AI technical leadership

Leading small AI engineering teams while staying hands-on.

#### Plain terms (portal CLIs)

```
AI Tech Lead
AI Team Lead
Lead AI Engineer
Lead GenAI Engineer
```

#### WebSearch-only (`site:` fallback)

```
site:linkedin.com/jobs "AI Tech Lead" OR "Lead AI Engineer" remote India
site:naukri.com "Tech Lead" "Generative AI" Bangalore
site:wellfound.com "Lead AI Engineer" remote
```

### Priority 3: Document AI / applied ML (domain expertise)

Intelligent document processing, insurance-tech AI, applied ML.

#### Plain terms (portal CLIs)

```
Document AI
Intelligent Document Processing
Machine Learning Engineer
Applied AI Engineer
```

#### WebSearch-only (`site:` fallback)

```
site:linkedin.com/jobs "intelligent document processing" OR "document AI" engineer India
site:naukri.com "Machine Learning Engineer" LLM Bangalore
site:linkedin.com/jobs "insurtech" "AI Engineer" India
```

### Priority 4: Broader AI / adjacent roles

Wider net: solutions and forward-deployed roles that use the same skills, plus Python backend with AI.

#### Plain terms (portal CLIs)

```
AI Solutions Engineer
Forward Deployed Engineer
Python AI Developer
RAG
```

#### WebSearch-only (`site:` fallback)

```
site:linkedin.com/jobs "Forward Deployed Engineer" OR "AI Solutions Engineer" India remote
site:naukri.com "Python" "LangChain" developer Bangalore
site:remoteok.com "AI Engineer"
```

## Location Filter

Candidate lives in Bangalore. Preference order: **remote > hybrid > on-site**.
- **Ideal:** fully remote (India, or global-remote that hires from India)
- **Acceptable:** hybrid in Bangalore
- **Borderline:** on-site in Bangalore (acceptable but lowest preference)
- **Flag for discussion:** hybrid/on-site in other Indian cities (Hyderabad, Pune, Chennai, NCR) - requires relocation
- **Too far / exclude:** "remote" roles restricted to US/EU residents only; on-site abroad without sponsorship

**Salary floor:** 20 LPA minimum - flag postings whose stated range tops out below this.

## Language Filter

Your working languages and levels are in CLAUDE.md's Languages table. When filtering scraped results, apply `04-job-evaluation.md`'s Language Gate: a posting requiring a language you haven't declared at all is excluded; a posting requiring a higher level than you declared in a language you do work in is not excluded, flag it clearly instead (see `job-scraper/SKILL.md`'s Step 3 "Quick Fit Assessment" for how the flag surfaces in `/scrape` output). Postings simply *written* in a language you don't work in, that don't require it on the job, are fine.

## Date Filter

Only include jobs posted within the last 14 days, matching `/scrape` Step 1b.3: use a supported recency filter or filter the returned publication dates client-side. A future application deadline does not bypass this discovery window; `/rank` handles deadline expiry and urgency separately after discovery. If a posting date cannot be determined, include it but flag as "date unknown".

## Adapting Queries

If the user specifies a focus area, select queries from the matching category and also generate 2-3 custom queries for that focus. For example:
- "/scrape [focus_area]" -> relevant category queries + custom focus-specific queries
