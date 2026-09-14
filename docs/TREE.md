# MindsHub Documentation Tree

```
📚 MindsHub Documentation
│
├── 📖 README.md
│   └── Overview, quick start, build instructions
│       ├─→ Get started (web, macOS, Windows, Linux)
│       ├─→ Features & capabilities
│       └─→ Build from source
│
├── 🏗️ Development Documentation
│   │
│   ├── 📋 VIBE.md — Environment Model
│   │   └── Launch → Work → Keep → Destroy
│   │       ├─→ Disposable containers (blast radius = one box)
│   │       ├─→ Docker Compose workflow
│   │       ├─→ Recovery procedures
│   │       │   ├─→ `docker compose down -v && up`
│   │       │   ├─→ `make flush` (hard reset)
│   │       │   ├─→ `make baseline` (sync to pins)
│   │       │   └─→ Schema drift fixes
│   │       └─→ Windows & cloud-sync folder warnings
│   │
│   ├── 📋 CLAUDE.md — Superproject Workflow
│   │   └── Submodules, pins, multi-developer coordination
│   │       ├─→ Submodule structure (4 repos)
│   │       ├─→ Branch management (dev.env)
│   │       ├─→ Pin management (make pin)
│   │       ├─→ Running locally
│   │       │   ├─→ Web browser mode
│   │       │   └─→ Keycloak auth bypass
│   │       ├─→ Windows/WSL2 setup
│   │       └─→ Cloud-sync corruption recovery
│   │
│   └── 📋 docs/INDEX.md — Navigation Hub
│       └── Central reference for all documentation
│           ├─→ Quick start by task
│           ├─→ Recovery guide
│           ├─→ File structure reference
│           └─→ FAQ
│
├── 🤖 Agent Governance
│   │
│   └── 📋 .claude/agents/cowork-dev.md — Agent Rules
│       └── Behavioral boundaries & workflow
│           ├─→ Enforced Boundaries
│           │   ├─→ Never move pins
│           │   ├─→ Never disable tests
│           │   ├─→ Never commit dev.env
│           │   └─→ Never leave local paths
│           ├─→ When to Stop & Ask
│           │   ├─→ Pin bumps needed
│           │   ├─→ Multi-module changes
│           │   ├─→ Data migrations
│           │   └─→ Credential handling
│           └─→ Workflow (Locate → Reproduce → Verify → Push)
│
├── 🔧 Configuration
│   │
│   ├── .devcontainer/devcontainer.json
│   │   └── VS Code container setup
│   │       ├─→ Dockerfile: docker/api.Dockerfile
│   │       ├─→ Ports: 26866 (API), 5173 (web)
│   │       └─→ Git feature
│   │
│   ├── dev.env (gitignored)
│   │   └── Branch refs per developer
│   │       ├─→ REF (all modules)
│   │       ├─→ FRONTEND_REF
│   │       ├─→ API_REF
│   │       ├─→ AGENT_REF
│   │       └─→ VAULT_REF
│   │
│   ├── Makefile
│   │   └── Build & run commands
│   │       ├─→ make setup
│   │       ├─→ make dev / make dev-web
│   │       ├─→ make use
│   │       ├─→ make pin
│   │       ├─→ make flush
│   │       └─→ make baseline
│   │
│   └── docker-compose.yml
│       └── Stack services
│           ├─→ API (FastAPI)
│           ├─→ Web (Vite)
│           └─→ Database volume
│
├── 📂 Submodules (ignore = all)
│   │
│   ├── frontend/
│   │   └── Electron + Vite UI
│   │       ├─→ Tag-pinned in superproject
│   │       └─→ Tracked: mindsdb/cowork
│   │
│   ├── backend/core_api/
│   │   └── FastAPI server
│   │       ├─→ Tag-pinned in superproject
│   │       └─→ Tracked: mindsdb/cowork-server
│   │
│   ├── backend/core_agent/
│   │   └── Anton agent harness
│   │       ├─→ Tag-pinned in superproject
│   │       └─→ Tracked: mindsdb/anton
│   │
│   └── backend/data-vault/
│       └── Connections & credentials
│           ├─→ main-branch pinned
│           └─→ Tracked: mindsdb/data-vault
│
└── 📚 External Resources
    ├─→ Website: https://mindshub.ai/
    ├─→ Official Docs: https://docs.mindshub.ai/
    ├─→ Web App: https://console.mindshub.ai/
    ├─→ Discord: https://mindshub.ai/discord
    └─→ GitHub: https://github.com/mindsdb/minds
```

---

## How to Use This Tree

### By Role

**🚀 Just starting out?**  
→ Start with **README.md** → **VIBE.md** (environment model) → **docs/INDEX.md** (navigation)

**💻 Developing a feature?**  
→ **CLAUDE.md** (branching workflow) → **.claude/agents/cowork-dev.md** (boundaries) → **Makefile** (commands)

**🆘 Something broke?**  
→ **VIBE.md** (recovery procedures) → **CLAUDE.md** (Windows/cloud-sync fixes) → **docs/INDEX.md** (FAQ)

**🤖 Writing or managing agents?**  
→ **.claude/agents/cowork-dev.md** (rules) → **VIBE.md** (disposable environment) → **CLAUDE.md** (submodule safety)

### By Task

| Task | Primary Doc | Secondary | Tertiary |
|------|-------------|-----------|----------|
| Clone & setup | README.md | CLAUDE.md | — |
| First run | VIBE.md | README.md | docs/INDEX.md |
| Create feature branch | CLAUDE.md | dev.env.example | — |
| Commit & push | CLAUDE.md | .claude/agents/cowork-dev.md | — |
| Move submodule pin | CLAUDE.md | Makefile | .claude/agents/cowork-dev.md |
| Reset to pins | VIBE.md | Makefile | — |
| Fix corruption | CLAUDE.md | VIBE.md | — |
| Configure VS Code container | .devcontainer/devcontainer.json | README.md | — |
| Develop locally without pushing | CLAUDE.md (server-local) | .claude/agents/cowork-dev.md | — |

---

## File Dependencies

```
docs/INDEX.md (navigation hub)
├─ README.md (entry point)
├─ VIBE.md (environment)
│  └─ docker-compose.yml
│  └─ Makefile (down, flush, baseline)
├─ CLAUDE.md (workflow)
│  ├─ dev.env (configuration)
│  ├─ .gitmodules (submodule config)
│  ├─ Makefile (use, pin, server, app)
│  └─ backend/core_api/pyproject.toml ([tool.uv.sources])
└─ .claude/agents/cowork-dev.md (agent rules)
   ├─ VIBE.md (knows the environment)
   └─ CLAUDE.md (knows the workflow)
```

---

## Key Concepts Across Docs

### 🔐 Isolation & Safety

| Concept | In VIBE.md | In CLAUDE.md | In cowork-dev.md |
|---------|-----------|-------------|------------------|
| Disposable box | ✅ (core premise) | — | — |
| Blast radius | ✅ (one container) | — | — |
| Keep work via git | ✅ | ✅ (push submodule first) | ✅ (push to keep) |
| Schema drift | ✅ | ✅ | ✅ (agent's responsibility) |
| Never lose data | ✅ (always push) | ✅ (never move pins) | ✅ (ask before flush) |

### 🎯 Workflow

| Stage | VIBE.md | CLAUDE.md | cowork-dev.md |
|-------|---------|-----------|---------------|
| **Launch** | `docker compose up` | `make setup` | — |
| **Work** | `make dev` / `make dev-web` | Check out branch with `make use` | Reproduce issue first |
| **Keep** | `git push` | Push submodule, then `make pin` | Push changes, push to keep |
| **Destroy** | `docker compose down -v` | `make flush` | Don't call unprompted |

### 🚫 Hard Boundaries

All three documents enforce these together:

1. **Never move pins silently** — CLAUDE.md + cowork-dev.md
2. **Never disable tests** — cowork-dev.md
3. **Never commit dev.env** — CLAUDE.md + cowork-dev.md
4. **Always push before destroy** — VIBE.md + CLAUDE.md
5. **Never leave local paths in pyproject.toml** — CLAUDE.md + cowork-dev.md

---

Last updated: September 2025
