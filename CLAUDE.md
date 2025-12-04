# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Promethean Soup is a mentions feed for ideas. Users follow papers, books, or blogs, and the system discovers who's discussing them across the web. Think Twitter but following *works* instead of *people*.

**Stack:**
- **Frappe** (Python) - Full stack: user accounts, DocTypes, API, UI
- **Exa** - Web search API for finding mentions

## Development Environment

Use the devcontainer from [frappe_docker](https://github.com/weavermarquez/frappe_docker) `psoup` branch:

```bash
# Clone repos side-by-side
git clone https://github.com/weavermarquez/frappe_docker.git
git clone https://github.com/weavermarquez/promethean-soup.git
cd frappe_docker
git checkout psoup

# Copy devcontainer config
cp -R devcontainer-example .devcontainer

# Open in VSCode → "Reopen in Container"
```

Inside the devcontainer:
```bash
cd ~/frappe-bench
bench get-app promethean_soup /workspace/promethean-soup
bench --site <site_name> install-app promethean_soup
bench start
```

## Architecture

```
User follows work (URL, DOI, or title)
    ↓
Frappe: creates Followed Work DocType record
    ↓
Frappe: calls Exa API to search for mentions
    ↓
Frappe: stores results as Mention DocType records
    ↓
Feed UI: displays mentions for user's followed works
```

## DocTypes

**Followed Work** - Works a user is tracking
| Field     | Type   | Description                      |
|-----------|--------|----------------------------------|
| user      | Link   | Frappe user                      |
| work_url  | Data   | URL, DOI, or canonical ID        |
| title     | Data   | Display title (optional)         |

**Mention** - Discussions found about a work
| Field     | Type   | Description                      |
|-----------|--------|----------------------------------|
| work      | Link   | Reference to Followed Work       |
| url       | Data   | Where the mention lives          |
| title     | Data   | Page/post title                  |
| snippet   | Text   | Relevant excerpt                 |
| published | Date   | Publication date (if available)  |

## Key Files

```
promethean_soup/
├── __init__.py                    # Version
├── hooks.py                       # Frappe app config
├── api.py                         # Whitelisted API endpoints
├── promethean_soup/
│   └── doctype/
│       ├── followed_work/         # Followed Work DocType
│       └── mention/               # Mention DocType
├── public/                        # Frontend assets
└── templates/pages/               # Web pages (feed UI)
```

## Development Commands

```bash
# Run development server
bench start

# Run tests
bench --site <site_name> run-tests --app promethean_soup

# Build frontend assets
bench build

# Access Frappe console
bench --site <site_name> console
```

## Issue Tracking

Uses Beads (`bd` CLI). Run `bd onboard` at session start.

```bash
bd ready              # Find available work
bd create --title="..." --type=task|bug|feature
bd update <id> --status=in_progress
bd close <id>
bd sync               # Sync with git remote
```
