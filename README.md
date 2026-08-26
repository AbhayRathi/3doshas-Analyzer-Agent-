# 3Doshas Trend Intelligence MVP

## 1) MVP objective

Build one continuous loop that:

1. Collects current conversations from selected sources.
2. Groups related content into trends.
3. Scores trends for momentum and relevance to 3Doshas.
4. Applies air, fire, and water content lenses.
5. Produces a daily shortlist of 3–5 opportunities.
6. Records posts published by the team.
7. Tracks their performance over time.
8. Uses those results to improve future recommendations.

### MVP non-goals

The MVP will **recommend content opportunities**. It will **not**:

- Generate final posts
- Publish automatically
- Contact people
- Send marketing emails

## 2) Recommended stack

| Component | Choice |
|---|---|
| Language | Python 3.12 |
| Database | PostgreSQL through Supabase |
| ORM and migrations | SQLAlchemy 2 + Alembic |
| Validation | Pydantic |
| HTTP requests | HTTPX |
| AI analysis | OpenAI API |
| Scheduling | GitHub Actions initially |
| Daily delivery | Google Sheets first, email second |
| Testing | Pytest |
| Formatting and linting | Ruff |
| Type checking | Pyright |
| Dependency management | uv |
| Configuration | YAML files plus environment variables |
| CI | GitHub Actions |

Not needed for v1: FastAPI, custom frontend, vector database, Airflow, multi-service architecture.

## 3) Repository setup

Suggested repository name: `3doshas-trend-intelligence`

Operational requirements:

- Repository should be **private**
- `main` branch should be **protected**

Suggested structure:

```text
3doshas-trend-intelligence/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       ├── collect.yml
│       ├── daily-brief.yml
│       └── metrics.yml
├── config/
│   ├── brand.example.yaml
│   ├── audiences.yaml
│   ├── problems.yaml
│   ├── profiles.yaml
│   └── sources.yaml
├── docs/
│   ├── architecture.md
│   ├── scoring.md
│   ├── energy-framework.md
│   └── source-policy.md
├── migrations/
├── scripts/
│   ├── collect.py
│   ├── analyze.py
│   ├── generate_brief.py
│   ├── collect_metrics.py
│   └── run_pipeline.py
├── src/
│   └── three_doshas/
│       ├── collectors/
│       ├── database/
│       ├── processing/
│       ├── scoring/
│       ├── energy/
│       ├── briefs/
│       ├── analytics/
│       ├── models/
│       └── settings.py
├── tests/
│   ├── collectors/
│   ├── processing/
│   ├── scoring/
│   ├── energy/
│   └── analytics/
├── .env.example
├── .gitignore
├── pyproject.toml
└── README.md
```

## 4) Brand configuration

Convert internal materials into editable config (not hardcoded prompts):

```yaml
brand:
  name: Three Doshas
  positioning: Executive coaching through energy-based self-understanding

content_pillars:
  - leadership
  - executive performance
  - burnout
  - decision-making
  - team dynamics
  - personal alignment
  - sustainable growth

energy_lenses:
  air:
    strengths: []
    challenges: []
    motivations: []
    useful_interventions: []

  fire:
    strengths: []
    challenges: []
    motivations: []
    useful_interventions: []

  water:
    strengths: []
    challenges: []
    motivations: []
    useful_interventions: []
```

Fill from:

- Internal decks
- Charts
- Assessment questions
- Website
- Expert-provided examples

The classification rubric should be reviewed by a Three Doshas methodology expert.

## 5) Initial audiences and problems

Start with up to 5 audiences:

- Founders and entrepreneurs
- Corporate executives
- People and HR leaders
- Sales leaders
- Product and creative leaders

Start with ~10 recurring problems:

- Burnout
- Decision fatigue
- Lack of focus
- Resistance to change
- Inconsistent execution
- Team conflict
- Leadership transitions
- Overwork
- Loss of motivation
- Difficulty creating structure

All editable via YAML config.

## 6) Data sources

### MVP sources

- **X**: keyword/problem search; collect public text, timestamps, authors, engagement; monitor selected creators/companies.
- **Reddit**: monitor selected subreddits; collect recurring questions/frustrations/language; use as qualitative research, not copy.
- **Publications & RSS**: leadership, executive coaching, workplace, wellness, business sources.
- **Manual competitor watchlist**: selected LinkedIn and other social URLs.

### Later expansion

- LinkedIn company-page APIs (post approval)
- Instagram professional insights
- Google Trends or approved trend provider
- Commercial social listening tools (if needed)

Each source must have its own collector; one collector failure must not block others.

## 7) Database schema

Core tables:

- `sources`
- `monitored_profiles`
- `raw_items`
- `topics`
- `topic_items`
- `topic_scores`
- `energy_analysis`
- `recommendations`
- `published_posts`
- `metric_snapshots`
- `brief_runs`
- `job_runs`

`raw_items` requirements:

- Unique constraint on `(source_id, external_id)`
- `content_hash` for duplicate prevention

`brief_runs` and `job_runs` store delivery history, counts, failures, and error messages.

## 8) Scoring system

Initial weighted rules:

- 30% brand relevance
- 25% growth velocity
- 15% engagement quality
- 15% audience fit
- 10% originality opportunity
- 5% safety and confidence

Code computes momentum/engagement. LLM handles semantic judgments (brand relevance, audience fit, energy interpretation).

Every recommendation must include source links and written rationale.

## 9) Trend processing pipeline

Each collection run should:

1. Fetch new items.
2. Normalize data.
3. Remove exact and near duplicates.
4. Extract topics, problems, roles, industries.
5. Group semantically similar items.
6. Calculate engagement and growth.
7. Score relevance to 3Doshas.
8. Apply air/fire/water scores.
9. Generate candidate recommendations.
10. Save everything with source references.

Start with embeddings + similarity thresholds, or keyword/LLM grouping for small datasets.

## 10) Daily brief

Daily brief contains **3–5 recommendations**. Each includes:

- Trend title
- What is happening
- Evidence + source links
- Target role/industry
- Problem being discussed
- Air/fire/water scores
- Recommended primary lens
- Three Doshas interpretation
- Suggested hook
- Suggested format
- Urgency
- Confidence score

Example:

- Topic: Founder burnout after rapid growth
- Audience: Startup founders
- Problem: Burnout
- Primary lens: Fire
- Opportunity score: 86/100
- Publish within: 24–48 hours

Delivery destination: Google Sheets first; email generated from same data later.

## 11) Published-post intake

Use a Published Posts sheet with:

- Post URL
- Platform
- Publication time
- Recommendation ID
- Audience
- Problem
- Energy lens
- Format
- Hook

A scheduled job imports new rows to PostgreSQL.

## 12) Performance tracking

Capture metrics at:

- ~2 hours
- 24 hours
- 72 hours
- 7 days

Calculate:

- Engagement rate = engagements / impressions
- Share rate = shares / impressions
- Save rate = saves / impressions
- Comment rate = comments / impressions
- Follow conversion = followers gained / profile visits
- Click rate = clicks / impressions

Compare each post against:

- Trailing 30-day account median
- Same energy lens posts
- Same audience posts
- Same problem posts
- Comparable public competitor posts (when available)

Use rule-based scoring adjustments first; defer ML until enough history exists.

## 13) Scheduling (Pacific Time)

| Job | Frequency |
|---|---|
| X collection | Every 4 hours |
| Reddit collection | Every 6 hours |
| RSS collection | Every 6 hours |
| Trend processing | After collection |
| Daily brief | 7:00 a.m. |
| Post metrics | Every 2–4 hours |
| Weekly performance report | Monday morning |

Jobs should be idempotent.

## 14) Testing requirements

TDD for core behavior. Required coverage:

- API response normalization
- Duplicate prevention
- Topic grouping
- Score calculations
- Energy-score validation
- Recommendation filtering
- Metric calculations
- Database constraints
- Failure and retry behavior
- Mocked external APIs

Unit tests should not make live API calls.

CI should require:

- `pytest`
- `ruff check`
- `ruff format --check`
- `pyright`

## 15) Secrets and environment

Create `.env.example` placeholders:

```env
DATABASE_URL=
OPENAI_API_KEY=

X_CLIENT_ID=
X_CLIENT_SECRET=
X_BEARER_TOKEN=

REDDIT_CLIENT_ID=
REDDIT_CLIENT_SECRET=
REDDIT_USER_AGENT=

GOOGLE_SHEETS_CREDENTIALS_JSON=
GOOGLE_SHEET_ID=

BRIEF_RECIPIENT_EMAIL=
```

Store real credentials in local env vars and GitHub encrypted secrets.

## 16) Implementation milestones

1. **Foundation**: repo structure, CI, config models, DB schema/migrations, test fixtures.  
   Completion: CI passes and DB creates from scratch.
2. **First collection source** (RSS or Reddit): normalize + dedupe + tests.  
   Completion: scheduled run reliably stores new items without duplicates.
3. **Trend intelligence**: extraction/grouping/scoring/energy/recommendations.  
   Completion: stored items produce ranked recommendations.
4. **Daily brief**: Google Sheets output, formatting, delivery/failure logs.  
   Completion: team receives 3–5 useful recommendations automatically.
5. **Performance loop**: published-post intake, snapshots, baselines, weekly summary.  
   Completion: recommendation links to published post and measured results.
6. **Source expansion**: add X, more publications, evaluate LinkedIn, expand watchlist.  
   Completion: source growth improves recommendation quality without reliability loss.

## 17) What to prepare

Before implementation:

- Private GitHub repository
- Python 3.12
- Supabase project
- Three Doshas decks/charts/questions/website materials
- Social account URLs
- 5–10 competitor/creator URLs
- Initial target audiences and industries
- Google Sheet for daily briefs
- OpenAI, X, and Reddit credentials (when available)

After creating the empty repository: start with **Milestone 1**, then implement one source end-to-end before integrating additional sources.
