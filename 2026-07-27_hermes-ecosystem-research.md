# Hermes Agent Ecosystem Research — Professional Agency Setup on WSL2

**Date:** 2026-07-27
**Scope:** MCP servers, plugins, skills, WSL2 specifics, alternative tools for a 7-profile agency (NexoPC, NVIDIA NIM glm-5.2)
**Current state:** 21 MCP servers configured, state.db = 612 MiB (642,093,056 bytes — confirmed via `stat`)

---

## Current Inventory (confirmed from ~/.hermes/config.yaml)

**Enabled (19):** github, context7, playwright, firecrawl, ccxt, linear, filesystem, duckduckgo, sequential-thinking, pollinations, docker, dokploy, postgres, sentry, visual-parity, agent-lsp, lsp-intelligence, zenith, mcp-code-review-pro, blender

**Disabled (1):** memory-local (correctly disabled — Hermes has native memory tool)

**Dead weight to prune (matches your stated problems):**
- `memory-local` — disabled (good). Redundant with Hermes native `memory` tool. **Delete entry.**
- `duckduckgo` — redundant. Hermes has `web_search`. **Delete entry.**
- `sentry` — `@sentry/mcp-server` npx package no longer exists upstream (archived to servers-archived). You reported no creds. **Delete entry.**
- `dokploy` — `@dokploy/mcp` requires `DOKPLOY_URL` + `DOKPLOY_API_KEY` you don't have. **Delete entry.**
- `sequential-thinking` — Hermes has a native `sequential-thinking` MCP tool already in the deferred catalog. **Delete entry** (or keep if you find the MCP version more capable — low impact either way).
- `pollinations` — local custom server. Keep only if you actively use it for image generation. Otherwise prefer `image_generate` toolset.

**Estimated cleanup:** remove 4–6 entries → faster startup, fewer connection retries on the 403 rotation.

---

## state.db VACUUM (612 MiB)

Confirmed: `/home/sebas/.hermes/state.db` = 642,093,056 bytes. VACUUM with Hermes stopped:

```bash
# 1. Stop the gateway first to avoid WAL conflicts
hermes gateway stop   # or: pkill -f "hermes" + verify
# 2. Backup
cp /home/sebas/.hermes/state.db /home/sebas/.hermes/state.db.bak-$(date +%Y%m%d)
# 3. VACUUM
sqlite3 /home/sebas/.hermes/state.db "VACUUM;"
# 4. Check new size
ls -lh /home/sebas/.hermes/state.db
```

Expected: 612 MiB → 150–300 MiB depending on FTS5 fragmentation. Have a backup; VACUUM is safe but rewrites the whole file.

---

# 1. MCP Servers — Best-in-class by category

## 1.1 Code Development (LSP, code review, refactoring, debugging)

### agent-lsp (beruang/lsp-mcp) — ✅ KEEP, you have it
- **URL:** https://github.com/beruang/lsp-mcp
- **What:** 59 read-only LSP tools across TS/JS/Python/Go/Rust — navigation, diagnostics, safe refactoring (preview-only diffs), call/type hierarchy, impact analysis. Workspace-containment guard, no file writes, method denylist.
- **Why better:** Strict read-oriented safety model. `lsp_rename_preview` returns diffs but never applies. 106+ tests. V4 added server lifecycle, diagnostics snapshots, observability.
- **You already have it configured** (`agent-lsp` server, TypeScript backend).
- **WSL2:** ✅ Native — stdio, runs in WSL2, talks to `typescript-language-server` on PATH.
- **Config (already present):**
```yaml
agent-lsp:
  command: agent-lsp
  args: [typescript:typescript-language-server,--stdio]
  enabled: true
```
- **Recommended upgrade:** add Python + Go backends so it covers e14/synthetic-trader too:
```yaml
agent-lsp:
  command: agent-lsp
  args:
    - typescript:typescript-language-server,--stdio
    - python:pyright-langserver,--stdio
  enabled: true
```

### github/github-mcp-server — ✅ UPGRADE to official (replaces `@modelcontextprotocol/server-github`)
- **URL:** https://github.com/github/github-mcp-server (31K stars, Go, official)
- **What:** GitHub's official MCP server. Repo/file/issue/PR/CI/secret-scanning/code-search. OAuth 2.1 with PKCE (token in memory, never on disk) OR PAT. Remote URL `https://api.githubcopilot.com/mcp/`.
- **Why better than the archived `@modelcontextprotocol/server-github`:** That npm package was deprecated April 2025. The official Go server has 100× more tools (issues, PRs, code search, Actions, secret scanning, notifications, discussions), OAuth login (no PAT rotation), lockdown/read-only modes, and per-toolset filtering.
- **WSL2:** ✅ Three options:
  - **Remote (easiest):** `url: https://api.githubcopilot.com/mcp` + `auth: oauth` — works from WSL2, no binary.
  - **Docker:** `docker run -i --rm -e GITHUB_PERSONAL_ACCESS_TOKEN ghcr.io/github/github-mcp-server` (Docker Desktop on WSL2).
  - **Native binary:** build with Go 1.24+ or download release — runs in WSL2.
- **Config (recommended — remote + OAuth):**
```yaml
github:
  url: https://api.githubcopilot.com/mcp/
  auth: oauth
  enabled: true
  tools:
    include: [list_issues, create_issue, update_issue, search_code, list_pull_requests, get_pull_request, create_pull_request_review, merge_pull_request]
```
- **Config (alternative — local Docker + PAT, works headless):**
```yaml
github:
  command: docker
  args: [run, -i, --rm, -e, GITHUB_PERSONAL_ACCESS_TOKEN, ghcr.io/github/github-mcp-server]
  env:
    GITHUB_PERSONAL_ACCESS_TOKEN: ${GITHUB_TOKEN}
  enabled: true
```

## 1.2 Alternative code review — better than mcp-code-review-pro

### troshenkov/code-review-mcp-server — consider as supplement
- **URL:** https://github.com/troshenkov/code-review-mcp-server (FastMCP, archived/portfolio but deterministic tools still work)
- **What:** `senior_review`, `review_code_quality`, `security_review`, `refactor_code`, `generate_tests`. Wraps Ruff/ShellCheck/ESLint with secret-pattern checks.
- **Why vs mcp-code-review-pro:** mcp-code-review-pro focuses on GitHub PR workflow (diff + inline comments). troshenkv adds file-local security/quality/refactor static analysis the LLM can call mid-edit. **Use both:** mcp-code-review-pro for PR gates, troshenkov for pre-commit self-review. The latter is archived — fork if you extend.
- **WSL2:** ✅ Python, `uvx code-review-mcp-server`.
- **Config:**
```yaml
code-review:
  command: uvx
  args: [code-review-mcp-server]
  enabled: true
```

### Better strategy: LSP-driven review over MCP review
For the agency, the highest-leverage review path is `agent-lsp` diagnostics + Hermes' own `code-review-and-quality` / `requesting-code-review` skills (which you already have in your 229 skills). MCP code review servers add overhead; the skills + LSP give you per-stack awareness. Keep `mcp-code-review-pro` for the PR-commenting surface only.

## 1.3 Documentation — Context7 ✅ KEEP, you have it

- **URL:** https://github.com/upstash/context7 (also `@upstash/context7-mcp`)
- **What:** Live, version-pinned library docs pulled into context. `resolve-library-id` + `get-library-docs`/`query-docs`. Stops hallucinated APIs.
- **Why better than scraping docs:** Maintained index, version-specific, indexed from source. Used by Cursor/Claude/Continue.
- **WSL2:** ✅ `npx -y @upstash/context7-mcp` (your current config) or remote: `url: https://mcp.context7.com/mcp` with `CONTEXT7_API_KEY` header.
- **You have it.** Consider upgrading to the remote URL for lower cold-start:
```yaml
context7:
  url: https://mcp.context7.com/mcp
  headers:
    CONTEXT7_API_KEY: ${CONTEXT7_API_KEY}   # free tier exists
  enabled: true
```

## 1.4 Git/GitHub workflow

Covered by `github/github-mcp-server` above. For GitLab/Bitbucket, the `mcp-sql` + `coding-mcp` pair below gives you a remote multi-project git+file+command surface.

### kieutrongthien/coding-mcp — for remote multi-project coding
- **URL:** https://github.com/kieutrongthien/coding-mcp
- **What:** Multi-project MCP server: expose repos on a host machine, agent browses files, patches, runs builds/tests/lint via allowlist. STDIO + HTTP, RBAC, audit logging, OpenTelemetry.
- **Why useful for the agency:** Deploy one `coding-mcp` on a remote host (or your NexoPC) → any of the 7 Hermes profiles can operate on any project without local clones. Safer than raw filesystem MCP — `apply_patch`, allowlisted commands, RBAC.
- **WSL2:** ✅ Python, runs in WSL2 or as HTTP server (bind to 127.0.0.1).
- **Config:**
```yaml
coding-mcp:
  command: uvx
  args: [coding-mcp]
  env:
    PROJECTS_ROOTS: /home/sebas/proyectos
    ENABLE_HTTP: "false"
    ENABLE_STDIO: "true"
    ALLOWED_COMMANDS: npm,pnpm,yarn,git,go,python3,pytest,vitest,tsc
  enabled: true
```

## 1.5 Testing & QA (browser automation, visual regression, accessibility)

### Playwright MCP — ✅ KEEP, you have it
- **URL:** https://github.com/microsoft/playwright-mcp
- **What:** Browser automation via accessibility snapshots (not pixels). 40+ tools: navigate, click, type, snapshot, screenshot, tabs, dialogs, file upload, tracing, video, PDF. Vision tools (coordinate-based) optional.
- **Why best in class:** Snapshot-based = deterministic, ~200–400 tokens per snapshot vs thousands for DOM, LLM-friendly, cross-browser (Chrome/Firefox/WebKit/Edge). Microsoft-maintained.
- **WSL2:** ⚠️ You point `--executable-path` at a Linux Chromium (`/home/sebas/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome`) — that works, but for testing against your real signed-in Chrome on Windows, see WSL2 section below.
- **Config (your current — keep):**
```yaml
playwright:
  command: npx
  args:
    - -y
    - '@playwright/mcp'
    - --executable-path
    - /home/sebas/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome
  enabled: true
```

### Visual regression: visual-parity-mcp ✅ KEEP, or use Playwright MCP's screenshot+diff
- You have `visual-parity-mcp` configured. It does reference-vs-candidate URL/route comparison.
- Playwright MCP alone can do visual regression via `browser_take_screenshot` + pixel-diff helpers (cryptokishan/visual-ui-mcp-server wraps this with `pixelmatch` if you want a dedicated tool).
- **Recommendation:** Keep `visual-parity` for design parity audits. For per-commit UI regression, use your existing skill `visual-regression-testing` (Playwright screenshots + AI validation). Don't add a third tool.

## 1.6 Database access

You have `@modelcontextprotocol/server-postgres` but it's been **archived** (moved to servers-archived). Replace with one of:

### Recommended: mcp-sqlalchemy (woonstadrotterdam)
- **URL:** https://github.com/woonstadrotterdam/mcp-sqlalchemy (PyPI `mcp-sqlalchemy`)
- **What:** SQLite + PostgreSQL + MySQL via SQLAlchemy. Schema discovery, FK relationships, read-only by default. Solid, actively published (v0.2.4 Oct 2025).
- **Why better than the archived reference server:** Active maintenance, multi-engine, FK relationship mapping, `READ_ONLY_MODE` enforced.
- **WSL2:** ✅ `uvx "mcp-sqlalchemy[postgresql]"` — asyncpg, native WSL2.
- **Config:**
```yaml
sqlalchemy:
  command: uvx
  args: ["mcp-sqlalchemy[postgresql]"]
  env:
    DATABASE_URL: postgresql://user:pass@localhost:5432/mydb
    READ_ONLY_MODE: "true"
  enabled: true
```

### Alternative: lorenzouriel/sql-mcp (8 engines in one)
- **URL:** https://github.com/lorenzouriel/sql-mcp
- **What:** MSSQL, PostgreSQL, MySQL, MariaDB, SQLite, MongoDB, Databricks, Fabric. Multi-connection (up to 20). Read-only default with per-engine destructive pattern blocklists. Three output formats.
- **Config:**
```yaml
sql-mcp:
  command: uvx
  args: ["sql-mcp[postgres]"]
  env:
    SQL_MCP_ENGINE: postgres
    SQL_MCP_DSN: postgresql://user:pass@localhost:5432/mydb
  enabled: true
```
- Pick mcp-sqlalchemy for SQLite+Postgres simplicity. Pick sql-mcp if you need MongoDB/Databricks for trader work.

## 1.7 File system & project management

You have `@modelcontextprotocol/server-filesystem` mounted to `/home/sebas/proyectos`, `/home/sebas`, `/mnt/c/Users/sebas/Desktop`, `/mnt/c/Users/sebas/Downloads`. **It's useful but redundant** — Hermes has native `read_file`/`write_file`/`search_files`/`terminal`. Keep filesystem MCP only if you want explicit path-bounded sandboxing for delegated subagents. Otherwise remove and rely on Hermes native tools.

`zenith` and `lsp-intelligence` (which you have) are superior for code-aware file ops — keep those.

## 1.8 Terminal/shell enhancement

No MCP needed — Hermes has native `terminal`. For sandboxed/structured remote command execution, `coding-mcp` (above) wraps this with allowlists + RBAC.

## 1.9 Blender / 3D modeling

You have `blender-mcp==1.6.4` (ahujasid/blender-mcp, 18K+ stars). Two upgrade paths:

### Option A: zorak1103/blender-mcp (HTTP-native, cleaner)
- **URL:** https://github.com/zorak1103/blender-mcp
- **What:** MCP server + Blender add-on. Add-on exposes Streamable HTTP at `http://localhost:8400/mcp`. Tools: scene, objects, materials, render, shader nodes, modifiers, animation, lighting, camera, world.
- **Why better:** HTTP-native (mounts cleanly from WSL2 via portproxy — see WSL2 section), stdio proxy available, `execute_python` opt-in with documented security modes.
- **WSL2:** Needs Windows→WSL2 port forward for port 8400 (see WSL2 section).
- **Config:**
```yaml
blender:
  url: http://localhost:8400/mcp   # via portproxy from WSL2 to Windows
  enabled: true
```

### Option B: RFingAdam/mcp-blender (218 tools, most comprehensive)
- **URL:** https://github.com/RFingAdam/mcp-blender
- **What:** 218 tools — modeling, sculpting, animation, rigging, physics, geometry nodes, AI 3D generation (Hyper3D, Meshy, Tripo, Hunyuan3D, ComfyUI), MSFS livery pipeline.
- **Why choose:** If you do serious 3D/asset work. Overkill for occasional renders.

**Recommendation:** For your designer profile rendering mockups, keep `ahujasid/blender-mcp` (current) but switch the Blender addon to its TCP server so WSL2 Hermes can reach Windows Blender via portproxy. See WSL2 section.

## 1.10 Trading (ccxt, market data)

You have `@lazydino/ccxt-mcp`. Recommended upgrade path:

### doggybee/mcp-server-ccxt — best maintained CCXT MCP
- **URL:** https://github.com/doggybee/mcp-server-ccxt
- **What:** 20+ exchanges, spot/futures/swap. LRU caching (tickers 10s, orderbook 5s, markets 1hr). Proxy support, rate limiting. Read-only market data + private trading (with API keys).
- **Why better than @lazydino/ccxt-mcp:** More tools (leverage tiers, funding rates, batch tickers), documented caching, proxy support for geo-restricted exchanges, 1.6M+ downloads.
- **WSL2:** ✅ Node, `npx -y mcp-server-ccxt`.
- **Config:**
```yaml
ccxt:
  command: npx
  args: [-y, mcp-server-ccxt]
  env:
    BINANCE_API_KEY: ${BINANCE_API_KEY}
    BINANCE_API_SECRET: ${BINANCE_API_SECRET}
  enabled: true
```

### For read-only market research on your `trader` profile
`eliasfire617/crypto-market-data-mcp` is read-only (no keys, public endpoints) — safer for research subagents that shouldn't trade.

## 1.11 Security (pentesting, vulnerability scanning)

For your `ciberseguridad` profile + Kali VM:

### Vittal-Mukunda/MCP-Server-Pentest — most mature, structured
- **URL:** https://github.com/Vittal-Mukunda/MCP-Server-Pentest
- **What:** 20 OWASP Top 10 tools, 7-phase engagement lifecycle, scope enforcement on every call, MITRE ATT&CK auto-mapping, CVSS finding tracker, NVD CVE enrichment, audit-ready reports (MD/JSON/HTML). Plugin architecture.
- **Why best in class:** Only MCP server with **complete OWASP Top 10 coverage + engagement lifecycle management**. Scope enforcement is architecturally mandatory — prevents runaway scans. Comparison table in the repo shows it beats pentestMCP/pentest-mcp/mcp-pentest.
- **WSL2:** ✅ Python + FastMCP, stdio. Run from WSL2 against tools installed in WSL2 or your Kali VM.
- **Config:**
```yaml
pentest:
  command: uvx
  args: [mcp-server-pentest]   # or: python3 /path/to/pentest-server
  enabled: true
```

### KevMuir/kali-mcp-server — Dockercontainerized Kali toolkit
- **URL:** https://github.com/KevMuir/kali-mcp-server
- **What:** Containerized Kali with nmap/masscan/nuclei/nikto/sqlmap/hydra/metasploit/impacket/ffuf. SSE over HTTP. Reports mounted volume. `shell_command` escape hatch.
- **Why choose:** Cleaner isolation than running tools in WSL2 directly. Each scan is a fresh container.
- **WSL2:** ✅ Docker Desktop on WSL2.
- **Config:**
```yaml
kali-pentest:
  command: npx
  args: [--prefix, /path/to/kali-mcp-stdio, node, /path/to/kali-mcp-stdio/proxy.js]
  enabled: true
```

### CodingSelim/mcp-scan — MCP security auditor (meta-tool)
- **URL:** https://github.com/CodingSelim/mcp-scan
- **What:** **Passive** scanner for MCP servers. Audits any running MCP server against OWASP MCP Top 10 (2025), grades A–F. Never invokes tools — reads advertised capabilities statically.
- **Why essential:** Run this BEFORE wiring in any new MCP server. Endor Labs found 82% of servers prone to path traversal, 34% to command injection. Catches the "lethal trifecta" (reads private data + ingests untrusted content + can exfiltrate). **Use this as a gate in your orchestrator profile.**
- **WSL2:** ✅ `npx owasp-mcp-scan`.
- **Config (as an MCP server that scans MCPs — or run as CLI):**
```yaml
mcp-scan:
  command: npx
  args: [-y, owasp-mcp-scan]
  enabled: true
```

---

# 2. Hermes Plugins — Desktop, TUI, command palette

From https://hermes-agent.nousresearch.com/docs/developer-guide/desktop-plugin-sdk:

- **Plugin = single ESM file** dropped in `$HERMES_HOME/desktop-plugins/<id>/plugin.js`. App loads within seconds, hot-reloads on save. No build step.
- **SDK:** `@hermes/plugin-sdk` — UI kit (Button, Input, Tabs, Dialog, Badge, etc.), React Query, theme vars, `ctx.rest`/`ctx.socket` plugin backend namespace.
- **Surfaces you can contribute:**
  - `PANES_AREA` — layout panes (placement: left/right/bottom/main, dock, width, height)
  - `ROUTES_AREA` — full pages mounted in workspace
  - `SIDEBAR_NAV_AREA` — sidebar nav rows
  - `STATUSBAR_AREAS.left/.right` — status bar chips
  - `TITLEBAR_AREAS.left/.center/.right` — title bar tools
  - `PALETTE_AREA` — ⌘K command palette entries
  - `KEYBINDS_AREA` — rebindable keybinds
  - `THEMES_AREA` — custom desktop themes
  - `COMPOSER_AREAS.*` — composer slots / middleware

### Recommended agency desktop plugins to build (no community marketplace exists yet)

1. **Project switcher** (sidebar nav + palette) — list `~/proyectos/*`, click to cd the active session. Reads PROJECT.md on switch.
2. **Kanban board pane** (left pane) — live view of `~/.hermes/kanban/boards/<active>/` (you have 14 kanban boards including nexoaccmanager, e14-audit-platform, synthetic-trader). Move cards via drag.
3. **Profile switcher** (status bar chip) — shows active profile, click to switch among default/dev/orchestrator/research/designer/trader/ciberseguridad.
4. **Credential pool monitor** (status bar) — live count of active/cooling NVIDIA keys from auth.json (you have 7 keys round-robin).
5. **state.db size + VACUUM button** (status bar) — shows DB size; one-click VACUUM when > 500 MiB.
6. **MCP health panel** (bottom pane) — shows connection state of all 21 MCP servers, last error, restart button.

These are small writes to `~/.hermes/desktop-plugins/<id>/plugin.js` — your `default` profile can author them using the `hermes-desktop-plugins` skill (already bundled at `skills/hermes-desktop-plugins`).

### TUI widgets (`references/tui-widgets.md`)

For `hermes --tui` mode: live widget apps in `~/.hermes/tui-widgets/`. Same model — single `.mjs` file, hot-reloads. Use for ticker (trader), clock (default), dashboard (orchestrator).

### Catalog MCPs (`hermes mcp catalog`)

Hermes ships a curated catalog under `optional-mcps/` in the repo. **Presence = Nous approval.** `hermes mcp install <name>` does one-click install with credential prompts writing to `.env`. Run `hermes mcp catalog` to list approved entries, `hermes mcp` for the interactive picker.

---

# 3. Hermes Skills — installed and how to get more

### You already have 229 skills — best bundled ones for agency dev:

| Skill | Purpose | Profile |
|---|---|---|
| `hermes-agent` | Hub for setup/config/orchestration | all |
| `claude-code` / `codex` / `opencode` | Delegate to external coding agents | orchestrator, dev |
| `code-review-and-quality` | Multi-axis code review before merge | orchestrator |
| `requesting-code-review` / `receiving-code-review` | Code review lifecycle | orchestrator, dev |
| `spec-driven-development` / `spec-creation` / `spec-validation` | Spec-first workflow | orchestrator |
| `writing-plans` / `executing-plans` | Plan authoring + execution | orchestrator, dev |
| `kanban-orchestrator` / `kanban-worker` | Decomposition + anti-temptation rules | orchestrator, all |
| `dispatching-parallel-agents` | 2+ independent tasks parallelism | orchestrator |
| `systematic-debugging` / `debugging-and-error-recovery` | 4-phase root cause debugging | dev |
| `tdd` / `test-driven-development` / `vitest` | TDD workflow | dev |
| `typescript-error-fixing` | TS compile errors → zero | dev |
| `verification-before-completion` | Gate before "done" claims | all |
| `doubt-driven-development` | Fresh-context adversarial review | orchestrator |
| `finishing-a-development-branch` | Branch → merge → release | orchestrator |
| `lifecycle-methodology` | Full lifecycle for any project | orchestrator |
| `agent-instructions-management` | Manage global/local AGENTS.md | default |

### How skills are installed

1. **Agent-created skills** — `skill_manage` tool, saved to `~/.hermes/skills/`.
2. **In-repo skills** — follow `agent-skills` standard (`SKILL.md` frontmatter + body). See `hermes-agent-skill-authoring` skill you have.
3. **Community skills** — there is **no official Hermes skill marketplace yet**. Skills are shared via git repos / gists; install by cloning into `~/.hermes/skills/`. The `optional-mcps/` pattern (PR-merged catalog) is for MCPs, not skills.
4. **Profile isolation** — `~/.hermes/profiles/<name>/skills/` for per-profile skills.

### Skills to author for the agency (high-leverage, don't exist yet)

1. **`mcp-onboarding`** — given a new MCP candidate: run `mcp-scan` to grade, write minimal config block, restart with `/reload-mcp`, verify connection, document in AGENTS.md. Gates every new MCP adoption.
2. **`agency-dispatch`** — wraps your AGENTS.md dispatch table; given a task, classify + dispatch to the right profile via `delegate_task` or Kanban, enforce max-2-parallel.
3. **`state-db-maintenance`** — monthly VACUUM + size check, scheduled via cron.
4. **`release-orchestration`** — you have this skill; extend with your NVIDIA credential rotation awareness.

---

# 4. Windows / WSL2 Specifics

## 4.1 The fundamental WSL2 networking problem

Windows native apps (Blender, Chrome, Figma) bind to `127.0.0.1` on Windows. WSL2's `127.0.0.1` is a different loopback. **Default NAT mode** requires the Windows host IP, found from WSL2 via:
```bash
ip route show | grep -i default | awk '{ print $3 }'
# typical: 172.x.x.1
```

## 4.2 Best fix: Mirrored networking mode (Windows 11 22H2+)

In `%USERPROFILE%\.wslconfig`:
```ini
[wsl2]
networkingMode=mirrored
```
Then `wsl --shutdown` and restart. Result: **Windows servers reachable from WSL2 via `localhost`**. IPv6 support, VPN compatibility, multicast, LAN access to WSL — all fixed. This is the single most impactful WSL2 change for MCP.

**Caveat:** May require Hyper-V firewall rule:
```powershell
Set-NetFirewallHyperVVMSetting -Name '{40E0AC32-46A5-438A-A0B2-2B479E8F2E90}' -DefaultInboundAction Allow
```

## 4.3 Connecting MCP servers to Windows native apps

### Blender (Windows) ← WSL2 Hermes
Blender addon listens on `localhost:9876` (Windows) or `localhost:8400` (HTTP variant). With mirrored mode: WSL2 Hermes connects to `localhost:9876` directly. Without mirrored mode:
```powershell
# Windows admin PowerShell — find WSL IP
wsl hostname -I
# e.g. 172.30.96.100

# Forward WSL requests on port 9876 to Windows localhost:9876
netsh interface portproxy add v4tov4 listenport=9876 listenaddress=172.30.96.100 connectport=9876 connectaddress=127.0.0.1
netsh advfirewall firewall add rule name="Blender MCP WSL" dir=in action=allow protocol=TCP localport=9876
```
Hermes config:
```yaml
blender:
  url: http://172.30.96.100:8400/mcp   # WSL-visible IP
  enabled: true
```

### Chrome (Windows) ← WSL2 Hermes for Playwright
Reference: `rizonetech/ChromeMCP` and `USCGVet/MCP-PROXY`. Pattern: launch Chrome on Windows with `--remote-debugging-port=9222`, expose via portproxy, point Playwright MCP at it. Lets you drive your **real signed-in Chrome** from WSL2 (same cookies, same logins, multi-client).

```yaml
playwright-chrome:
  command: npx
  args: [-y, '@playwright/mcp', --cdp-endpoint, http://<windows_ip>:9222]
  enabled: true
```

### Figma (Windows) ← WSL2
Same portproxy pattern on `3845`. Documented at https://gist.github.com/QuentinFrc/1f07d3e7f4d867f85b86994e270f6608.

## 4.4 WSL2 performance optimization for Hermes

1. **Enable mirrored mode** (above) — kills the IP-lookup dance.
2. **Mount /home/sebas on the Linux filesystem** (you do — `/home/sebas/proyectos`), NOT on `/mnt/c/` (9p filesystem is 10× slower for node_modules/git). ✅ You're already doing this.
3. **For npm/npx MCPs:** pre-warm the npx cache so cold-start isn't 8s per server. Run `npx -y <pkg> --help` once for each MCP package.
4. **VACUUM state.db monthly** (see above) — 612 MiB slows FTS5 queries.
5. **Disable 4 dead MCPs** — saves ~15s startup and 4 × connection-retry churn.
6. **Docker MCPs:** if using `github-mcp-server` via Docker, use Docker Desktop's WSL2 backend (default) — no extra config.
7. **Wslg/Desktop:** Hermes desktop app works via WSLg on Windows 11. If you prefer native Windows, install Hermes on Windows side too — same `$HERMES_HOME` patterns, different profile prefix.

---

# 5. Alternative / Additional Tools

## 5.1 Multi-agent orchestration

### Hermes-native (you have this)
- `delegate_task` — bounded parallel subtasks within one process. **Your AGENTS.md caps at 2 parallel** (NVIDIA pool saturation). This is the right default.
- `dispatching-parallel-agents` skill.
- `kanban-orchestrator` + `kanban-worker` — for multi-session, persistent, profile-routed work.
- tmux + `hermes chat -q` for long autonomous missions (per `hermes-agent` skill: spawn independent `hermes` processes).

### External orchestrators (only if you outgrow Hermes-native)
- **Claude Code dynamic workflows** (script-driven, dozens–hundreds of agents, resumable) — works alongside Hermes via `claude-code` skill.
- **Claude Code agent teams** — peer-to-peer messaging, shared task list — experimental.
- **claude-swarm** (affaann-m) — TUI dashboard, Opus planning + Haiku workers, quality gate. **Don't adopt:** couples to Anthropic OAuth, conflicts with your NVIDIA-primary setup.
- **Hive** (ndpvt-web) — Aristotelian model-matching, contract-first parallel. Interesting pattern but Claude Code-bound.

**Recommendation:** Stay Hermes-native. Your 2-parallel cap is the binding constraint (NVIDIA keys), not orchestration sophistication. Add a `kanban-orchestrator` periodic review cronjob.

## 5.2 Better browser automation than Playwright

**Verdict: Playwright MCP is the best option.** Microsoft-maintained, snapshot-based (token-cheap), cross-browser. Alternatives (puppeteer-mcp, BrowserMCP) are inferior for LLM use because they're pixel/DOM-heavy. The only upgrade path is **Playwright MCP + Chrome-on-Windows via CDP** (see WSL2 section) — gives you real browser sessions.

## 5.3 Better debugging tools

- **`agent-lsp` diagnostics** (you have) — `lsp_diagnostics`, `lsp_wait_for_diagnostics`, `lsp_explain_diagnostics`, `lsp_fix_diagnostic_candidates`. This is the best MCP-level debugger.
- **node-inspect-debugpy skill** (you have `node-inspect-debugger` + `python-debugpy`) — for runtime debugging.
- **Add:** nothing. LSP + native skills cover it.

## 5.4 Better documentation generation tools

- **Context7** (have) — pulls docs IN.
- **For generating docs OUT:** your `api-documentation`, `architecture-documentation`, `code-wiki`, `documentation-and-adrs` skills cover it.
- **Add:** `mcp__github__create_or_update_file` from the official GitHub MCP server to publish generated docs to repos directly.

## 5.5 Tools for multi-agent orchestration — covered in 5.1.

---

# Priority Action Plan

## Immediate (today, 30 min)
1. **VACUUM state.db** (612 MiB) — stop gateway, backup, `sqlite3 ... VACUUM`.
2. **Prune dead MCPs:** remove `memory-local`, `duckduckgo`, `sentry`, `dokploy` from config.yaml.
3. **Enable mirrored WSL2 networking** — edit `.wslconfig`, `wsl --shutdown`.
4. **Upgrade to official `github/github-mcp-server`** — replace `@modelcontextprotocol/server-github` entry with remote URL + OAuth.

## This week (2 hours)
5. **Replace `@modelcontextprotocol/server-postgres`** with `mcp-sqlalchemy[postgresql]`.
6. **Upgrade `ccxt`** to `doggybee/mcp-server-ccxt`.
7. **Add `mcp-scan`** as the MCP adoption gate — run it on every new server before wiring.
8. **Wire Blender via portproxy** so Windows Blender is reachable from WSL2 Hermes.

## This month (ongoing)
9. **Build 3 desktop plugins** (project switcher, kanban pane, MCP health panel).
10. **Author `mcp-onboarding` skill** that codifies the scan → config → reload → verify gate.
11. **Add `agent-lsp` Python backend** for synthetic-trader / e14-fraud-detector.
12. **Schedule monthly state.db VACUUM** via cron.

---

# Confidence Levels

| Recommendation | Confidence | Basis |
|---|---|---|
| Prune 4 dead MCPs | 🟢 High | Confirmed config + upstream archive status |
| VACUUM state.db | 🟢 High | Confirmed 642MB size via stat |
| Upgrade to github/github-mcp-server | 🟢 High | Official, 31K stars, npm package deprecated Apr 2025 |
| mcp-sqlalchemy for DBs | 🟢 High | Active (v0.2.4 Oct 2025), multi-engine |
| Mirrored WSL2 mode | 🟢 High | Microsoft docs, Windows 11 22H2+ |
| doggybee ccxt | 🟡 probable | Most downloaded but @lazydino may suffice |
| Vittal-Mukunda pentest | 🟡 probable | Strong repo but compare with your Kali tooling |
| Desktop plugins (build, not install) | 🟡 probable | No community marketplace yet — you author them |
| agent-lsp Python backend | 🟢 High | Supported in repo, pyright required |

---

# Sources

- Hermes Agent docs: https://hermes-agent.nousresearch.com/docs/
- Hermes MCP config reference: https://hermes-agent.nousresearch.com/docs/reference/mcp-config-reference
- Hermes native MCP guide: https://hermes-agent.nousresearch.com/docs/user-guide/skills/bundled/mcp/mcp-native-mcp
- Hermes desktop plugin SDK: https://hermes-agent.nousresearch.com/docs/developer-guide/desktop-plugin-sdk
- Hermes desktop overview: https://hermes-agent.nousresearch.com/docs/user-guide/desktop
- agent-lsp: https://github.com/beruang/lsp-mcp
- GitHub official MCP: https://github.com/github/github-mcp-server
- Playwright MCP: https://github.com/microsoft/playwright-mcp + https://playwright.dev/docs/getting-started-mcp
- Context7: https://github.com/upstash/context7 + https://context7.com/
- mcp-sqlalchemy: https://github.com/woonstadrotterdam/mcp-sqlalchemy
- sql-mcp (8 engines): https://github.com/lorenzouriel/sql-mcp
- coding-mcp: https://github.com/kieutrongthien/coding-mcp
- code-review-mcp (troshenkov): https://github.com/troshenkov/code-review-mcp-server
- zorak1103/blender-mcp: https://github.com/zorak1103/blender-mcp
- ahujasid/blender-mcp: https://github.com/ahujasid/blender-mcp
- RFingAdam/mcp-blender (218 tools): https://github.com/RFingAdam/mcp-blender
- doggybee/ccxt: https://github.com/doggybee/mcp-server-ccxt
- crypto-market-data-mcp (read-only): https://github.com/eliasfire617/crypto-market-data-mcp
- MCP-Server-Pentest: https://github.com/Vittal-Mukunda/MCP-Server-Pentest
- kali-mcp-server: https://github.com/KevMuir/kali-mcp-server
- mcp-scan (OWASP MCP Top 10): https://github.com/CodingSelim/mcp-scan
- OWASP MCP Top 10: https://owasp.org/www-project-mcp-top-10/
- WSL2 networking: https://learn.microsoft.com/en-us/windows/wsl/networking
- WSL2 mirrored mode: https://learn.microsoft.com/en-us/windows/wsl/networking#mirrored-mode-networking
- WSL2 + Figma MCP guide: https://gist.github.com/QuentinFrc/1f07d3e7f4d867f85b86994e270f6608
- WSL2 + Chrome CDP (rizonetech/ChromeMCP): https://github.com/rizonetech/ChromeMCP
- WSL2 + Chrome CDP (USCGVet/MCP-PROXY): https://github.com/USCGVet/MCP-PROXY
- Claude Code dynamic workflows: https://code.claude.com/docs/en/workflows
- Claude Code agent teams: https://code.claude.com/docs/en/agent-teams
- MCP servers (official reference repo): https://github.com/modelcontextprotocol/servers
- MCP registry: https://registry.modelcontextprotocol.io/
- Awesome MCP lists: https://github.com/AlexMili/Awesome-MCP, https://github.com/appcypher/awesome-mcp-servers, https://github.com/korchasa/awesome-mcp
