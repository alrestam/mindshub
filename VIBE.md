# VIBE: running agents against this repo without burning the host

The premise, borrowed from [changelog.com's Genesis
blueprint](https://github.com/thechangelog/changelog.com/blob/master/VIBE.md): an
agent that can edit files, install packages and run servers needs somewhere it
can do that *wrongly*. Give it a disposable box, keep the good changes, throw
the box away.

Genesis does this with Incus VMs on a dedicated i9. This repo doesn't need that
much iron — the whole stack is a FastAPI server plus a Vite renderer — so the
isolation layer here is a **container**, and the "keep" step is **git**.

Two rules carry over unchanged:

- **Blast radius is one box.** When the agent goes astray, you delete the box,
  not the machine.
- **Only pushed work is kept work.** Anything that lives solely inside the
  disposable box is already lost.

## The rhythm

```
launch  →  work  →  keep  →  destroy  →  repeat
```

| Step | Command | What it means here |
|---|---|---|
| launch | `docker compose up` (or Dev Containers: *Reopen in Container*) | fresh api + web containers from `docker-compose.yml` |
| work | `make dev` / `make dev-web` | local submodule source, hot reload, agent edits freely |
| keep | `git commit && git push` on a module branch | the host mount is git; a pushed branch survives the box |
| destroy | `docker compose down -v` | containers **and** the `cowork-data` volume go away |

`down -v` is the important half. Without `-v` the `cowork-data` volume
persists, and a corrupted `cowork.db` follows you into the next "fresh" run.

## Three modules, three branches, no collisions

Genesis runs three agents in three VMs so they don't step on each other. The
equivalent boundary here is already in the repo: each module is a submodule with
`ignore = all`, and each gets its own ref.

```bash
cp dev.env.example dev.env
# FRONTEND_REF=feat/ui-thing
# API_REF=feat/server-thing
# AGENT_REF=feat/agent-thing
make use     # check them out
make refs    # confirm what the next run will use
```

Because `.gitmodules` sets `ignore = all`, an agent committing inside
`backend/core_api` never shows up as a superproject change. The pin moves only
when a human runs `make pin`. That is the whole anti-collision model — an agent
cannot silently change what everyone else builds.

## Recovering from a bad run

| Damage | Recovery |
|---|---|
| containers wedged / db corrupt | `docker compose down -v && docker compose up` |
| local installs half-broken | `make flush` then `make setup` |
| submodules on stray branches | `make baseline` |
| schema drift between app + dev server (`Can't locate revision …`) | `make flush` — the two paths share `~/.cowork/cowork.db` |

`make flush` is the hard reset: it deletes the `cowork-server` uv tool, both
`.venv`s, `~/.anton` (provider keys) and `~/.cowork` (database, projects). It
prompts first; `FORCE=1` skips the prompt. Treat it as the "relaunch the VM"
button — and know it takes your conversations and saved keys with it.

## Where the host is not disposable

Two failure modes here damage the *host*, not the box, so no amount of
container isolation saves you:

1. **Cloud-sync folders.** A repo inside OneDrive/Dropbox/Google Drive/iCloud
   can have its git objects truncated into unreadable packs. See
   [`CLAUDE.md`](CLAUDE.md#windows-and-cloud-sync-folders).
2. **Unpushed work.** `docker compose down -v` and `make flush` are both
   irreversible. Push the branch before either.

## On Windows

The `make` targets assume a POSIX shell. Run them inside WSL2, or stay on the
`docker compose` path, which works from PowerShell directly. Full breakdown in
[`CLAUDE.md`](CLAUDE.md#windows-and-cloud-sync-folders).

## The agent's own rules

The environment says where an agent may fail. What it may *decide* is a
separate document: [`.claude/agents/cowork-dev.md`](.claude/agents/cowork-dev.md).
