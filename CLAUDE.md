# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Promethean Soup is a mentions feed for ideas. Users follow papers, books, or blogs, and the system discovers who's discussing them across the web. Think Twitter but following *works* instead of *people*.

**Stack:**
- **Frappe** (Python) - User accounts, UI, DocTypes, Exa API bridge
- **Rama** (Java) - Dataflow backend handling follow events → search → store mentions (`forager/`)
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

The devcontainer includes:
- Frappe/bench environment
- Java 21 (Temurin) + Maven for forager development
- promethean-soup mounted at `/workspace/promethean-soup`

## Development Commands

**Frappe/Bench commands** (run from bench directory, typically `~/frappe-bench`):

```bash
# Run development server
bench start

# Run tests for this app
bench --site <site_name> run-tests --app promethean_soup

# Enable test mode on a site
bench --site <site_name> set-config allow_tests true

# Build frontend assets
bench build

# Install app to a site
bench --site <site_name> install-app promethean_soup

# Create a new DocType
bench --site <site_name> console  # then use frappe.new_doc()
```

**Initial setup:**
```bash
pip install frappe-bench
bench init ~/frappe-bench
cd ~/frappe-bench
bench get-app promethean_soup /path/to/this/repo
bench new-site mysite.localhost --db-root-password root --admin-password admin
bench --site mysite.localhost install-app promethean_soup
```

**Forager (Rama module) commands:**
```bash
cd /workspace/promethean-soup/forager

# Build JAR
mvn package

# Run tests
mvn test

# Clean and rebuild
mvn clean package
```

## Architecture

```
User action (follow work)
    ↓
Rama depot: user-actions → PState: followed-works, mentions-by-work
    ↓ HTTP call on follow
Frappe: search_mentions(work_url) → Exa API → returns Mentions
    ↓
Feed UI displays mentions for followed works
```

**Key data structures:**
- `user-actions` depot: `{user-id, action (:follow/:unfollow), work-url}`
- `followed-works` PState: `user-id → [work-url]`
- `mentions-by-work` PState: `work-url → [Mention]`
- `Mention`: `{url, title, snippet, date, work-url}`

## Frappe App Structure

```
promethean_soup/
├── __init__.py           # Version (0.0.1)
├── hooks.py              # Frappe app hooks configuration
├── modules.txt           # Module list
├── patches.txt           # Database migrations
├── config/               # App configuration
├── promethean_soup/      # Main module (DocTypes go here)
├── public/               # Frontend JS/CSS assets
└── templates/pages/      # Web pages
```

**Creating new features:**
- DocTypes: Create in `promethean_soup/promethean_soup/doctype/<doctype_name>/`
- API endpoints: Add `@frappe.whitelist()` functions in `api.py`
- Web pages: Add to `templates/pages/`

## Issue Tracking

Uses Beads (`bd` CLI). Run `bd onboard` at session start.

```bash
bd ready              # Find available work
bd create --title="..." --type=task|bug|feature
bd update <id> --status=in_progress
bd close <id>
bd sync               # Sync with git remote
```
