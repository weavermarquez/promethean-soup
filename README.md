# Promethean Soup 🔥🍲

> *Prometheus stole fire to cook up the primordial soup of knowledge.*

Follow works. See who's talking about them.

## What is this?

Promethean Soup is a mentions feed for ideas. You follow papers, books, or blogs you care about — and the system searches the web to find who's discussing them.

Think Twitter, but instead of following *people* who push content at you, you follow *works* and pull discourse about them from across the internet.

## User Story

1. User follows a paper (by URL, DOI, or title)
2. System searches for mentions across the web via Exa
3. User sees a feed of discussions about works they follow

**That's it.** No embeddings, no semantic search, no agentic research jobs. Just: follow → discover → read.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      FRAPPE (Python)                        │
│                                                             │
│  DocTypes:                 API:                             │
│  - Followed Work           @frappe.whitelist()              │
│  - Mention                 def search_mentions(work_url):   │
│                                → exa.search(...)            │
│                                                             │
│  UI: Feed view of mentions for followed works               │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                        EXA API                              │
│         Neural search for who's discussing a work           │
└─────────────────────────────────────────────────────────────┘
```

**Stack:**
- **Frappe**: User accounts, data storage, UI, API endpoints
- **Exa**: Web search API that finds discussions about a given work

## Data Model

### Followed Work

| Field     | Type   | Description                      |
|-----------|--------|----------------------------------|
| user      | Link   | Frappe user                      |
| work_url  | Data   | URL, DOI, or canonical ID        |
| title     | Data   | Display title (optional)         |

### Mention

| Field     | Type   | Description                      |
|-----------|--------|----------------------------------|
| work      | Link   | Reference to Followed Work       |
| url       | Data   | Where the mention lives          |
| title     | Data   | Page/post title                  |
| snippet   | Text   | Relevant excerpt                 |
| published | Date   | Publication date (if available)  |

## Development

See [CLAUDE.md](CLAUDE.md) for development environment setup and commands.

## Status

- [x] Name that makes me smile
- [x] Repo initialized
- [x] Frappe app scaffold
- [x] Development environment (devcontainer)
- [ ] Followed Work DocType
- [ ] Mention DocType
- [ ] Exa search endpoint
- [ ] Follow → search → store flow
- [ ] Feed UI
- [ ] Demo-ready with seed papers

## Future Ideas

- **Rama integration**: Dataflow backend for reactive updates and state management
- Twitter bot for mobile notifications
- Browser extension to inject mentions into feeds
- Periodic re-crawling for new mentions
- Embeddings for semantic similarity
- "Threads" grouping related mentions

## References

- [Frappe Framework](https://frappeframework.com/)
- [Exa API](https://exa.ai/)

## License

MIT
