# MindsHub Documentation Index

Welcome to the MindsHub development and deployment documentation. This is your central reference for understanding, developing, and operating the Minds Platform.

## Core Documentation

### 📋 Project Overview
- **[README.md](../README.md)** — Overview, getting started, build instructions
  - Links to web app, desktop clients, and deployment options
  - Quick start for all platforms (macOS, Windows, Linux)

### 🏗️ Architecture & Setup

#### Development Environment
- **[VIBE.md](../VIBE.md)** — Isolated development environment model
  - **What it explains**: `launch → work → keep → destroy` workflow
  - **When to read**: Before your first `docker compose up` or `make dev`
  - **Key concepts**:
    - Disposable containers (your blast radius is one box)
    - Only pushed work is kept work
    - Recovery procedures (`make flush`, `make baseline`)
    - Windows + cloud-sync folder warnings
  - **Best for**: Understanding the sandbox model, troubleshooting local environments

#### Project Configuration
- **[CLAUDE.md](../CLAUDE.md)** — Minds Platform superproject workflow
  - **What it explains**: Four submodules, branch management, pins, and multi-developer coordination
  - **When to read**: Before committing changes, especially across modules
  - **Key concepts**:
    - Submodule refs (`dev.env`, `make use`)
    - Pin management (`make pin` only)
    - Windows/WSL2 setup
    - Schema drift and flushing

---

## Agent Behavior & Governance

### 🤖 Cowork-Dev Agent Rules
- **[.claude/agents/cowork-dev.md](../.claude/agents/cowork-dev.md)** — Claude agent behavioral boundaries
  - **Enforced boundaries**: Never move pins, always push submodule before pointer, never disable tests, never commit `dev.env`
  - **When to stop and ask**: Pin bumps, cross-module changes, data migrations, credential handling
  - **Workflow**: Locate → reproduce → verify → minimal diff → push to keep
  - **Relevant to**: Both humans and agents working on feature branches

---

## Quick Navigation by Task

### Getting Started
1. Clone: `git clone --recurse-submodules https://github.com/mindsdb/minds.git`
2. Setup: `make setup`
3. Read: [VIBE.md](../VIBE.md) (environment model) → [CLAUDE.md](../CLAUDE.md) (workflow)
4. Run: `make dev` (Electron) or `make dev-web` (browser)

### Developing a Feature
1. Pick your branch: Copy `dev.env.example` → `dev.env`, set `API_REF=feat/my-thing`
2. Checkout: `make use` (follows `dev.env`)
3. Run: `make dev` or `make dev-web`
4. Commit inside submodule, push, then `make pin` in superproject
5. See: [CLAUDE.md § Working on module branches](../CLAUDE.md#working-on-module-branches-multi-developer)

### Recovering from Issues
| Issue | Recovery | Docs |
|-------|----------|------|
| Container wedged, DB corrupt | `docker compose down -v && docker compose up` | [VIBE.md § Recovering](../VIBE.md#recovering-from-a-bad-run) |
| Local installs half-broken | `make flush` then `make setup` | [CLAUDE.md § Flushing](../CLAUDE.md#flushing-local-installs-fresh-start) |
| Schema drift (`Can't locate revision`) | `make flush` (dev + app share `~/.cowork/cowork.db`) | [VIBE.md § Schema drift](../VIBE.md#schema-drift) |
| Cloud-sync corruption (`bad object HEAD`) | Re-clone outside synced folder | [CLAUDE.md § Windows and cloud-sync](../CLAUDE.md#windows-and-cloud-sync-folders) |

### Working with Agents
- Agents follow [cowork-dev.md](../.claude/agents/cowork-dev.md) rules
- Agents will not: move pins, disable tests, run destructive commands unprompted, commit `dev.env`
- Agents will ask when: pin bumps needed, multiple modules affected, data migrations required

---

## File Structure Reference

```
mindshub/
├── README.md                          ← Start here for overview
├── CLAUDE.md                          ← Submodule workflow & Windows setup
├── VIBE.md                            ← Disposable environment model
├── .claude/
│   └── agents/
│       └── cowork-dev.md              ← Agent behavioral boundaries
├── frontend/                          ← Electron + Vite UI (submodule)
├── backend/
│   ├── core_api/                      ← FastAPI server (submodule)
│   ├── core_agent/                    ← Anton harness (submodule)
│   └── data-vault/                    ← Connections & credentials (submodule)
├── docker/
│   ├── api.Dockerfile
│   └── web.Dockerfile
├── .devcontainer/
│   └── devcontainer.json              ← VS Code container config
└── docs/
    └── INDEX.md                       ← You are here
```

---

## Frequently Asked Questions

**Q: What's the difference between VIBE.md and CLAUDE.md?**  
A: **VIBE.md** is about the *environment* (containers, volumes, the `make` workflow, recovery). **CLAUDE.md** is about the *codebase* (submodules, pins, branch coordination, Windows setup).

**Q: Why does `make dev` use local source but the desktop app doesn't?**  
A: `make dev`/`dev-web` run from your current checkout. The Electron app installs `cowork-server` via `uv tool install` — you need `make server` to pick up branch changes, or `make server-local` for uncommitted changes. Keep `COWORK_SERVER_REF` and your checked-out branch in sync to avoid schema drift.

**Q: Can I work on multiple modules at once?**  
A: Yes — per-module `*_REF` in `dev.env` (e.g., `API_REF=feat/server`, `AGENT_REF=feat/agent`). The superproject's `ignore = all` keeps `git status` clean. `make pin` only moves pins you deliberately set.

**Q: Will an agent move my submodule pins?**  
A: No — agents follow [cowork-dev.md](../.claude/agents/cowork-dev.md) and will explicitly ask before any pin change.

**Q: What if I clone into OneDrive/Dropbox/Google Drive?**  
A: Don't — cloud-sync can corrupt `.git` objects. See [CLAUDE.md § Windows and cloud-sync](../CLAUDE.md#windows-and-cloud-sync-folders) for details and recovery.

---

## Related Resources

- **[Discord Community](https://mindshub.ai/discord)** — Ask questions, share ideas
- **[Official Docs](https://docs.mindshub.ai/)** — API, deployment, architecture
- **[GitHub Issues](https://github.com/mindsdb/minds/issues)** — Report bugs, request features
- **[Blog](https://mindshub.ai/blog)** — Updates and announcements

---

*Last updated: September 2025*  
*See also: [.claude/agents/cowork-dev.md](../.claude/agents/cowork-dev.md) for agent behavioral rules*
