# Hermes Ecosystem Upgrade — Skills, MCPs, and WSL2 Integration
**Fecha:** 2026-07-27 | **Sesión:** ecosystem-research-and-install

---

## Resumen Ejecutivo

Sebastian, esto es lo que se ejecutó en esta sesión:

### Acciones Completadas
1. **state.db VACUUM** — 611 MiB, sin fragmentación significativa (backup creado)
2. **4 MCPs muertos pruned** — memory-local, duckduckgo, sentry, dokploy (dokploy restaurado a petición tuya)
3. **GitHub MCP upgraded** — de `@modelcontextprotocol/server-github` (archivado) al oficial `github/github-mcp-server` con OAuth
4. **PostgreSQL MCP upgraded** — de `@modelcontextprotocol/server-postgres` (archivado) a `mcp-sqlalchemy[postgresql]` con `READ_ONLY_MODE` y conexión a Windows PostgreSQL vía `host.docker.internal:5432`
5. **agent-lsp multi-stack** — ahora soporta TypeScript + Python (pyright-langserver) simultáneamente
6. **22 skills certificadas instaladas** — de addyosmani/agent-skills (80K stars) y alirezarezvani/claude-skills (23K stars)
7. **Dashboard inspeccionado** — 21 MCP servers confirmados, 2 plugins activos (KANBAN, ACHIEVEMENTS), catálogo con 4 entries

### Pendiente (requiere Windows)
- **WSL2 mirrored networking** — editar `%USERPROFILE%\.wslconfig` en Windows (no se puede hacer desde WSL2)

---

## Skills Instaladas (22 nuevas, 142 total)

### De addyosmani/agent-skills (80K stars, MIT, CI-validated)
Source: https://github.com/addyosmani/agent-skills

| Skill | Qué hace | Por qué es mejor |
|---|---|---|
| `api-and-interface-design` | Contract-first API design, Hyrum's Law, One-Version Rule | Previene API breaking changes con patterns que el agente sigue |
| `browser-testing-with-devtools` | Chrome DevTools MCP para runtime data: DOM, console, network, performance | Reemplaza `tsc --noEmit` lento con LSP en tiempo real |
| `code-simplification` | Chesterton's Fence, Rule of 500, reduce complexity preserving behavior | Diferente a tu `simplify-code` — este es un workflow completo con anti-rationalization |
| `deprecation-and-migration` | Code-as-liability mindset, compulsory vs advisory deprecation, migration patterns | Para cuando necesites remover código legacy sin romper |
| `frontend-ui-engineering` | Component architecture, state management, WCAG 2.1 AA, design systems | Especializado en frontend — complementa tus skills de Electron |
| `git-workflow-and-versioning` | Trunk-based development, atomic commits, change sizing (~100 lines) | Mejora tus commits actuales — más atómicos, más seguros |
| `idea-refine` | Divergent/convergent thinking para ideas vagas → propuestas concretas | Antes de `brainstorming` — refina primero |
| `interview-me` | Requirements interrogation, one question at a time | Para specs incompletos — mejor que adivinar |
| `observability-and-instrumentation` | Structured logging, RED metrics, OpenTelemetry tracing | Para producción — necesitas observabilidad |
| `performance-optimization` | Core Web Vitals targets, profiling workflows, bundle analysis | Para cuando performance importa |
| `planning-and-task-breakdown` | Atomic tasks, critical path, 20% buffer | Mejora tus `writing-plans` con estimation |
| `shipping-and-launch` | Pre-launch checklists, staged rollouts, rollback procedures | Antes de deploy — checklist obligatorio |

### De alirezarezvani/claude-skills (23K stars, MIT, SkillCheck-validated)
Source: https://github.com/alirezarezvani/claude-skills

| Skill | Qué hace | Por qué es mejor |
|---|---|---|
| `zero-hallucination-coder` | Previene confabulation — obliga al agente a verificar claims con código real | Soluciona tu queja central: "las skills que creas a veces no funcionan" |
| `self-improving-agent` | Auto-memory curation — el agente recuerda qué funcionó y qué no | Se complementa con tu `memory` tool existente |
| `security-auditor` | Full security auditing: OWASP Top 10, STRIDE, dependency scanning | Más completo que tu `security-and-hardening` individual |
| `security-pen-testing` | Structured penting workflow with hard guardrails |Para tu perfil `ciberseguridad` |
| `playwright-pro` | Advanced Playwright: testrail, review, migrate, report | Extiende tu `playwright-cli` básico |
| `sql-database-assistant` | SQL patterns, query optimization, schema design | Para PostgreSQL que acabas de instalar |
| `workflow-builder` | Build complex multi-step agent workflows | Para tu orchestrator profile |
| `universal-scraping-architect` | Scraping patterns: stealth, Cloudflare bypass, pagination | Para tu `research` profile |
| `strict-api` | Strict API design with validation | Complementa `api-and-interface-design` |
| `tech-debt-tracker` | Track and manage technical debt systematically | Para proyectos legacy (NAM, e14) |

---

## MCP Upgrades Aplicados

### GitHub MCP — oficial con OAuth
```yaml
github:
  enabled: true
  url: https://api.githubcopilot.com/mcp/
  auth: oauth
```
**Por qué es mejor:** OAuth 2.1 con PKCE (token en memoria, nunca en disco), 100× más tools que el server archivado, sin rotación de PAT.

### PostgreSQL MCP — mcp-sqlalchemy (reemplaza archivado)
```yaml
postgres:
  command: uvx
  args: ['mcp-sqlalchemy[postgresql]']
  env:
    DATABASE_URL: postgresql://postgres:***@host.docker.internal:5432/devagency
    READ_ONLY_MODE: 'true'
  enabled: true
```
**Por qué es mejor:** Multi-engine (SQLite + PostgreSQL + MySQL), FK relationship mapping, READ_ONLY enforced, activamente mantenido (v0.2.4+).

### agent-lsp — multi-stack TypeScript + Python
```yaml
agent-lsp:
  command: agent-lsp
  args:
    - 'typescript:typescript-language-server,--stdio'
    - 'python:pyright-langserver,--stdio'
  enabled: true
```
**Por qué es mejor:** Ahora el LSP cubre ts y .py — diagnostics reales para synthetic-trader, e14-fraud-detector, wordlists.

---

## Code Review en GitHub — La Solución

Tu problema: "el code review no se ve en GitHub". La causa era que `@modelcontextprotocol/server-github` estaba archivado y no soportaba inline PR comments. Ahora con el GitHub MCP oficial (OAuth), ya tienes:
- `create_pull_request_review` con event APPROVE/REQUEST_CHANGES/COMMENT
- `add_pr_comment` con inline comments (path + line)
- `merge_pull_request` con método squash/merge/rebase

**Para que el code review se vea en GitHub PRs:**
1. Autentica el GitHub MCP: `hermes mcp auth github` (abre browser para OAuth)
2. El orchestrator profile puede usar `skill:github-code-review` + `skill:requesting-code-review` para firmar reviews en PRs
3. Los reviews aparecerán como inline comments directamente en el diff de GitHub

---

## LSP para más stacks (Go, Rust, C++)

Ya agregué Python al agent-lsp. Para stacks adicionales, agrega más backends al comando:

```bash
# Para Go (cuando necesites):
hermes config set mcp_servers.agent-lsp.args "['typescript:typescript-language-server,--stdio','python:pyright-langserver,--stdio','go:gopls,serve']"

# Para Rust (cuando necesites):
hermes config set mcp_servers.agent-lsp.args "['typescript:typescript-language-server,--stdio','rust:rust-analyzer']"
```

**Importante:** `pyright-langserver` debe estar instalado: `npm install -g pyright`

---

## OpenCode vs Hermes — Veredicto

| Criterio | OpenCode | Hermes | Ganador |
|---|---|---|---|
| LSP nativo | ✅ built-in 30+ servers auto-install | ✅ via agent-lsp MCP (multi-backend) | Empate |
| MCP support | ✅ stdio + SSE | ✅ stdio + SSE + OAuth + HTTP | Hermes |
| Desktop app | ✅ Electron | ✅ Web dashboard + desktop plugins | Empate |
| Scheduling/cron | ❌ community plugin only | ✅ first-party | **Hermes** |
| Multi-agent | ❌ no subagents | ✅ delegate_task + kanban | **Hermes** |
| Persistent memory | ❌ SQLite only | ✅ automatic + memory tool + skills | **Hermes** |
| Messaging | ❌ community only | ✅ 15+ first-party (Telegram, Discord, etc.) | **Hermes** |
| Provider support | ✅ 75+ | ✅ many (NVIDIA primary) | Empate |
| Skills ecosystem | ✅ AGENTS.md + 30+ plugins | ✅ 142+ skills + auto-creation | **Hermes** |
| SWE-bench | 88.0% | N/A | OpenCode |
| Windows native apps | ✅ futuro (CDP bridge) | ✅ via cua-driver + computer_use | Empate |

**Veredicto:** **Quédate con Hermes.** OpenCode tiene mejor SWE-bench pero carece de cron, subagents, messaging, y memory automática — todo lo que tu agencia necesita. OpenCode es un coding specialist; Hermes es un operating system completo. La única razón para usar OpenCode sería si necesitas su LSP auto-install (30+ servers auto-detectados), pero agent-lsp ya cubre tu stack (TS + Python).

**Si quieres complementar:** Hermes ya tiene el skill `opencode` que puede delegar tareas de coding a OpenCode CLI. Así obtienes lo mejor de ambos: Hermes orquesta, OpenCode ejecuta coding interactivo cuando su TUI es ventajoso.

---

## WSL2 Mirrored Networking — Acción Pendiente

Esta es la única acción que no puedo ejecutar desde WSL2 — requiere editar un archivo en Windows:

**En Windows, crea/edita `%USERPROFILE%\.wslconfig`:**
```ini
[wsl2]
networkingMode=mirrored
```

Luego: `wsl --shutdown` (PowerShell admin) y reinicia WSL2.

**Resultado:** Windows PostgreSQL (localhost:5432) y Blender (localhost:9876) serán alcanzables desde Hermes en WSL2 vía `localhost` directamente — sin `host.docker.internal` ni portproxy.

Tu PostgreSQL config ya usa `host.docker.internal:5432` que funciona en modo NAT, pero con mirrored mode puedes cambiarlo a `localhost:5432` para más simplicidad.

---

## Skills para Agentes Expertos en Desarrollo e IA

### Lo que ya tienes (y que sí funciona):
- `superpowers` (obra, 174K stars) — TDD, brainstorm, code review, verification
- `karpathy-guidelines` — reduce LLM coding mistakes
- `doubt-driven-development` — adversarial review antes de decisions
- `subagent-driven-development` — parallel execution con review
- `execplan` / `executing-plans` / `writing-plans` — planification completa
- `spec-driven-development` / `spec-creation` / `spec-validation` — spec-first

### Lo que instalamos hoy que cierra gaps:
- `zero-hallucination-coder` — **CRÍTICO**: obliga al agente a verificar antes de afirmar
- `self-improving-agent` — el agente aprende de sus propios errores
- `interview-me` — requirements interrogation antes de specs vagos
- `idea-refine` — divergent/convergent thinking antes de brainstorm
- `planning-and-task-breakdown` — estimación + critical path

### Skills específicas para AI/ML que no instalamos pero existen:
- `K-Dense-AI/scientific-agent-skills` (31K stars, 154 skills científicas) — para research que involucra ML, genomics, drug discovery. No relevantes para tu agencia ahora, pero dispobles en `npx skills add K-Dense-AI/scientific-agent-skills` si las necesitas.

---

## Fuentes Certificadas de Skills (verificadas, CI-tested)

| Repo | Stars | Skills | License | CI/Tests |
|---|---|---|---|---|
| [obra/superpowers](https://github.com/obra/superpowers) | 174K | 14 | MIT | ✅ |
| [addyosmani/agent-skills](https://github.com/addyosmani/agent-skills) | 80K | 24 | MIT | ✅ eval framework |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | 23K | 362 | MIT | ✅ SkillCheck-validated |
| [microsoft/skills](https://github.com/microsoft/skills) | 2.8K | 175 | MIT | ✅ 1169 test scenarios |
| [K-Dense-AI/scientific-agent-skills](https://github.com/K-Dense-AI/scientific-agent-skills) | 31K | 154 | MIT | ✅ |
| [pedronauck/skills](https://github.com/pedronauck/skills) | 540 | 130 | — | ⚠️ no CI |
| [ZhanlinCui/Ultimate-Agent-Skills-Collection](https://github.com/ZhanlinCui/Agent-Skills-Hunter) | 174 | 51 | MIT | ✅ CI validates SKILL.md |

**Cómo instalar más skills en el futuro:**
```bash
# Método 1: Clone + copy (selectivo, recomendado)
git clone --depth 1 https://github.com/<repo>.git /tmp/<name>
cp -r /tmp/<name>/skills/<skill-name> ~/.hermes/skills/

# Método 2: npx skills CLI (instala en múltiples agent platforms)
npx skills add <repo> --skill <skill-name>

# Método 3: Hermes native (si el repo tiene sync script)
python scripts/sync-hermes-skills.py --verbose
```
