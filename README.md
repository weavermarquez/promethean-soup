# Promethean Soup 🔥🍲

> *Prometheus stole fire to cook up the primordial soup of knowledge.*

Follow works. See who's talking about them.

Follow papers and blogs. See who's talking about them. See what Prometheus was cooking.


## What is this?

Promethean Soup is a mentions feed for ideas. You follow papers, books, or blogs you care about — and the system crawls the web to find who's discussing them.

Think Twitter, but instead of following *people* who push content at you, you follow *works* and pull discourse about them from across the internet.

## Demo Scope (AI Tinkerers, Dec 2024)

**User story:**
1. User follows a paper (by URL, DOI, or title)
2. System searches for mentions across the web
3. User sees a feed of discussions about works they follow

**That's it.** No embeddings, no semantic search, no agentic research jobs. Just: follow → discover → read.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                        RAMA (JVM)                           │
│                                                             │
│  Depot: user-actions     PState: followed-works             │
│  {:user-id, :action,     user-id → [work-url]               │
│   :work-url}                                                │
│                          PState: mentions-by-work           │
│                          work-url → [Mention]               │
│                                    │                        │
│                          HTTP call │ (on follow)            │
└────────────────────────────────────┼────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────┐
│                      FRAPPE (Python)                        │
│                                                             │
│  @frappe.whitelist()              DocTypes:                 │
│  def search_mentions(work_url):   - Followed Work           │
│      → exa.search(...)            - Mention                 │
│                                                             │
│  UI: Feed view of mentions        Exa Python SDK            │
│      for followed works                                     │
└─────────────────────────────────────────────────────────────┘
```

**Why this stack?**
- **Rama**: Handles the dataflow (follow events → trigger search → store mentions) with built-in state management
- **Frappe**: Provides user accounts, UI scaffolding, and a Python runtime to call Exa's SDK
- **Exa**: The actual search — finds who's talking about a given work across the web

## Data Model

### Depot: user-actions

| Field    | Type   | Example                          |
|----------|--------|----------------------------------|
| user-id  | String | "guest" or frappe user id        |
| action   | Enum   | :follow, :unfollow               |
| work-url | String | DOI, URL, or canonical identifier|

### PState: followed-works

```
user-id: String → [work-url: String]
```

### PState: mentions-by-work

```
work-url: String → [Mention]
```

### Mention

| Field    | Type   | Description                    |
|----------|--------|--------------------------------|
| url      | String | where the mention lives        |
| title    | String | page/post title                |
| snippet  | String | relevant excerpt               |
| date     | String | ISO date or null               |
| work-url | String | the work being mentioned       |

## Monorepo Structure

```
promethean-soup/
├── README.md
├── rama/                 # Rama backend (JVM/Clojure)
│   ├── src/
│   └── pom.xml
├── frappe-app/           # Frappe custom app (Python)
│   ├── promethean_soup/
│   │   ├── doctype/
│   │   └── api.py        # Exa bridge endpoints
│   └── setup.py
└── deploy/               # Deployment configs
    └── docker-compose.yml (or similar)
```

## Status

- [x] Name that makes me smile
- [ ] Repo initialized
- [ ] Rama depot + pstate skeleton
- [ ] Frappe app scaffold
- [ ] Exa search endpoint
- [ ] Follow → search → store flow working
- [ ] Feed UI
- [ ] Demo-ready with 3 seed papers

## Future Ideas (post-demo)

- Twitter bot that posts mentions to a private account (native mobile notifications!)
- Browser extension to inject mentions into your actual Twitter feed
- Periodic re-crawling for new mentions (alerts mode)
- Embeddings for semantic similarity
- "Threads" grouping related mentions

## References

- [Rama Documentation](https://redplanetlabs.com/docs/~/index.html)
- [Frappe Framework](https://frappeframework.com/)
- [Exa API](https://exa.ai/)


## License

mit
