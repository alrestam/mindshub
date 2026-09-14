# Documentation map

Four documents cover this repo. Each answers a different question — this page says which one to open.

| Document | Answers |
|---|---|
| [`README.md`](../README.md) | What is this, how do I install or build it |
| [`VIBE.md`](../VIBE.md) | Where may an agent fail safely, and how do I reset when it does |
| [`CLAUDE.md`](../CLAUDE.md) | How do submodules, refs and pins work; Windows setup |
| [`.claude/agents/cowork-dev.md`](../.claude/agents/cowork-dev.md) | What an agent may decide on its own, and when it must stop |

`VIBE.md` is the environment; `CLAUDE.md` is the codebase; `cowork-dev.md` is the judgment. They are deliberately separate.

## By task

| Task | Start at |
|---|---|
| First build from source | [`README.md` § Build from source](../README.md#build-from-source) |
| Understand the disposable-box model | [`VIBE.md` § The rhythm](../VIBE.md#the-rhythm) |
| Work on a module branch | [`CLAUDE.md` § Working on module branches](../CLAUDE.md#working-on-module-branches-multi-developer) |
| Push a submodule change | [`CLAUDE.md` § Make changes and push inside a submodule](../CLAUDE.md#make-changes-and-push-inside-a-submodule) |
| Move a pin | [`CLAUDE.md` § Working on module branches](../CLAUDE.md#working-on-module-branches-multi-developer) — `make pin` only, and only after the module PR merged |
| Recover a broken environment | [`VIBE.md` § Recovering from a bad run](../VIBE.md#recovering-from-a-bad-run) |
| Run an agent against this repo | [`cowork-dev.md`](../.claude/agents/cowork-dev.md) |

## Ports

Three different ports, three different run paths — the usual source of "why is nothing on localhost".

| Run path | URL | What serves it |
|---|---|---|
| `make dev-web` | `http://localhost:5173/` | Vite dev server, hot reload, local submodule source |
| `docker compose up` | `http://localhost:3000/` | nginx serving a built SPA (`docker/web.Dockerfile`) |
| either | `http://127.0.0.1:26866/` | FastAPI (`core_api`) |

The Vite path needs `VITE_SKIP_AUTH=true` in `frontend/src/renderer/.env` to bypass the SSO redirect — see [`CLAUDE.md` § Skip Keycloak auth](../CLAUDE.md#skip-keycloak-auth-required-for-local-dev).

## Refs

`dev.env` (gitignored, copy from `dev.env.example`) selects the branch each module is checked out on:

```bash
REF=feat/my-thing          # all modules
FRONTEND_REF=feat/ui       # per-module overrides win over REF
API_REF=feat/server
AGENT_REF=feat/agent
```

`data-vault` has no ref variable — it tracks `main`. `make refs` prints what the next run will use; `make use` checks them out.

## Destructive commands

Both are irreversible and both take conversations and provider keys with them. Push first.

| Command | Destroys |
|---|---|
| `make flush` | `cowork-server` uv tool, both `.venv`s, `~/.anton` (keys), `~/.cowork` (database, projects) |
| `docker compose down -v` | containers **and** the `cowork-data` volume |

`docker compose down` without `-v` keeps the volume — which means a corrupted `cowork.db` follows you into the next "fresh" run. Details in [`VIBE.md`](../VIBE.md#recovering-from-a-bad-run).

## Layout

```
README.md                      overview, install, build
VIBE.md                        disposable-environment model
CLAUDE.md                      submodules, refs, pins, Windows
docs/INDEX.md                  this map
.claude/agents/cowork-dev.md   agent boundaries
.devcontainer/devcontainer.json  VS Code container (api.Dockerfile, ports 26866 + 5173)
docker-compose.yml             api (26866) + web (3000), cowork-data volume
Makefile                       setup use refs dev dev-web server app pin baseline flush …
frontend/                      Electron + Vite          → mindsdb/cowork        (tag-pinned)
backend/core_api/              FastAPI server           → mindsdb/cowork-server (tag-pinned)
backend/core_agent/            Anton harness            → mindsdb/anton         (tag-pinned)
backend/data-vault/            connections, credentials → mindsdb/data-vault    (main)
```
