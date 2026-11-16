# Análisis de Specs + Catálogo de Patrones de Diseño — Workspace completo

**Fecha**: 2026-09-29 · **Sesión**: `sess-cadca764ea8dcea9` (dark-memory) · **Operador**: sebas
**Alcance**: todos los proyectos en `Desktop\proyectos\` (27 dirs) + infra del harness en `C:\Users\sebas\opencode\` (dark-memory-src, dark-research-mcp, github-mcp-server).

---

## 1. Resumen ejecutivo

- **11 specs formales** (vibe_specs en dark-memory): sólo **3 son de producto real** (NX-Studio #7, MyMochis-Studio #10, MyMochis closure #11); los otros 8 fueron tests de pipeline (2026-09-11/14).
- **20 proyectos de código** + 7 dirs auxiliares. La mayoría usa la convención `PROJECT.md` (SSOT) + `AGENTS.md` + kit OSS — convención sana y uniforme.
- **Riesgo #1: proyectos sin git** — `mymochis-studio`, `nx-booking`, `nx-bzns`, `pcshield-launch` pueden perderse.
- **Riesgo #2: specs sin criterios verificables** — la mayoría de "specs" son READMEs descriptivos, no especificaciones testables (contra el pipeline vibe_spec → drift_judge que ya existe).
- **Riesgo #3: duplicados** — `triqui` y `Tic-Tac-Toe` son clones idénticos.
- **Riesgo #4: tests ausentes** en 9 de los 20 proyectos de código (mymochis, portafolio, grani-usco, nexocore, nva-demons, wifi-pentest, triqui/Tic-Tac-Toe, nx-bzns).

---

## 2. Catálogo de specs formales (vibe_specs, dark-memory)

| # | vibe_case | Intención | Tasks | Fecha | Veredicto |
|---|---|---|---|---|---|
| 1 | C2 | Validar vibe-loop E2E con juez MiniMax | 2 | 09-11 | pipeline OK; drift_judge falla por NLI ausente |
| 2–6 | C2/C2 | Reintentos de verificación del pipeline (mismos intents) | 0–2 | 09-11 | duplicados de test |
| 7 | **C4** | **NX-Studio — Landing profesional** (Astro 5 + Tailwind 4, 14 tasks, Lighthouse ≥90, sin backend) | 14 | 09-12 | spec real principal de NX-Studio |
| 8 | C1 | Verificación pipeline tras fix Bun preload (C1) | 4 | 09-14 | verificado 54/57 tools |
| 9 | C1 | Verificación pipeline variante artifact_ref | 0 | 09-14 | needs_human (NLI missing) |
| 10 | **C1** | **MyMochis-Studio — Stack OSS multi-tenant** (ADR-005: Neon branch-por-tenant, 9 tasks) | 9 | 09-23 | scaffolded; pendiente provisión Neon/Hetzner |
| 11 | **C1** | **MyMochis v2.0.0 — security lockdown** (register customer-only, admin seed env, config real, E2E, tag) | 5 | 09-28 | **cerrado**, T4 (E2E) pendiente |

> Nota: specs 2–6 son ruido de testing — candidatos a no referenciar en métricas de cobertura.

## 3. Specs informales por proyecto (ADRs, PROJECT.md, docs técnicas)

| Proyecto | Stack | Docs/specs existentes | Tests | Git |
|---|---|---|---|---|
| **multra-eu** v5.1.0 | Node 20 ESM, Express, SQLite/Neon dual, JWT, zod | README, PRODUCTION.md, ADR-005 ref (dark-mem #113/142) | 25 (server/test, node --test) | ✓ |
| **mymochis** v2.0.0 | Node 24, Express, node:sqlite, JWT/scrypt, Helmet | README, spec #11 (dark-mem) | ✗ | ✓ |
| **mymochis-studio** v0.2.0 | Node 20, Express, pg/Neon, multi-tenant | ARCHITECTURE.md (ADR-005), DEPLOY.md, spec #10 | smoke script | **✗** |
| **NX-Studio** v0.3.0 | Astro 7 SSG, Tailwind 4, TS, Cloudflare adapter | PROJECT.md, PORTFOLIO_CONCEPT.md, RESEARCH.md, spec #7 | Playwright E2E | ✓ |
| **portafolio** v0.1.1 | Next.js 16, React 19, MDX, next-intl, three/GSAP | README, PROJECT.md, docs/ARCHITECTURE.md | ✗ (gates: build+smoke) | ✓ |
| **NexoAccManager** v5.0.0 | Electron 30, React 18, Mantine, better-sqlite3, **hexagonal** | PROJECT.md, DESIGN-research-v3.md, MIGRATION.md | 103 vitest, 68.24% cov | ✓ |
| **flag-edge** | Go 1.22, gorilla, SSE, SQLite | docs/{API,SDK,ARCHITECTURE,REQUIREMENTS}.md | go test | ✓ |
| **supply-radar** v1.1 | Go 1.23 CLI zero-dep, OSV.dev | docs/{TECHNICAL_DESIGN,REQUIREMENTS,DISCOVERY,ARCHITECTURE}.md | go test | ✓ |
| **contract-guard** | Node+TS CLI, OpenAPI/GraphQL/proto diff | docs/{TECHNICAL_DESIGN,OPERATIONS,EXAMPLES,ARCHITECTURE}.md | vitest | ✓ |
| **synthetic-trader** v0.1.0 | Python 3.11, pandas/scipy, Deriv | REFACTORING_MASTER_PLAN.md, architecture_spec.json, 2 research docs | pytest | ✓ |
| **e14-fraud-detector** | Python 3.14, FastAPI, OpenCV, Tesseract, MVC | PROJECT.md, **PAUSED.md**, schemas SQL | scripts verificar_* (no suite) | ✓ |
| **nexocore** | Next.js 15, Prisma, NextAuth v5, BullMQ | PROJECT.md (README = template) | ✗ | ✓ |
| **nva-demons** | Next.js 16, R3F, three-vfx | PROJECT.md, docs/hievents-integration.md | ✗ | ✓ |
| **grani-usco** | Next.js 16, React 19, Tailwind 4 | PROJECT.md, kit OSS | ✗ | ✓ |
| **nx-booking** | Astro 7 + node adapter, Prisma 6 | CLAUDE.md (README = starter) | ✗ | **✗** |
| **pcshield-launch** | HTML/CSS/JS estático | PROJECT.md | ✗ | **✗** |
| **nx-bzns** | Node 22 CJS, Express | **ninguna** | ✗ | **✗** |
| **e14-audit-platform** | — | README 1 línea | ✗ | ✓ |
| **Tic-Tac-Toe / triqui** | PWA vanilla | PROJECT.md ×2 (idénticos) | ✗ | ✓ |
| **wifi-pentest** | Python + bash | PROJECT.md | ✗ | ✓ |
| **opencode infra** | dark-memory-src (Go fork), dark-research-mcp (Go), github-mcp-server (Go) | RFCs en vibe-flow/main/, docs/ ricos, JSON spec files | ✓ (Go) | ✓ |

### Gaps de cobertura de spec (criterios verificables)

| Nivel | Proyectos | Acción |
|---|---|---|
| **Spec formal testable (vibe_spec)** | NX-Studio #7, MyMochis-Studio #10, MyMochis #11 | mantener |
| **Spec/ADR informal fuerte** | multra-eu, flag-edge, supply-radar, contract-guard, NexoAccManager, synthetic-trader | promover secciones a criterios de aceptación |
| **Doc descriptiva, sin AC** | portafolio, grani-usco, nexocore, nva-demons, nx-booking, pcshield-launch | añadir bloque "Criterios de aceptación" al PROJECT.md |
| **Sin spec** | nx-bzns, e14-audit-platform, triqui | crear PROJECT.md mínimo o archivar |

---

## 4. Patrones de diseño recomendados

Research fundamentado (dark-memory runs 10–14 + web 2026-09-29): **Hexagonal/Ports & Adapters** para Node, **multi-tenant silo/pool/bridge**, **Repository + dual-DB adapter con contract tests**, single-source pricing engine.

### 4.1 Cross-cutting (todos los proyectos)

1. **Spec-First contract** — cada proyecto activo publica un vibe_spec (C1) con criterios de aceptación *machine-checkable*; el drift_judge evalúa contra él. Cierra el loop SDD que ya está construido en dark-memory.
2. **PROJECT.md como SSOT + bloque "Criterios de aceptación"** — formalizar lo que hoy es narrativo.
3. **Git obligatorio** — `git init` + remote para mymochis-studio, nx-booking, nx-bzns, pcshield-launch (planned data-loss risk).
4. **Contract tests de adapters** — donde hay dualidad (SQLite/Neon, disco/R2), un mismo test suite corre contra ambos adapters → garantiza Liskov entre implementaciones.
5. **ADR ligero numerado** (como ADR-005) para toda decisión estructural, referenciado en PROJECT.md.

### 4.2 Por proyecto

| Proyecto | Patrón recomendado | Por qué / evidencia | Esfuerzo |
|---|---|---|---|
| **multra-eu** | **Content-hash cache pattern** (skill ECC) para servicios/cotizaciones estáticas | Cotizador y catálogo casi inmutables → cache por SHA-256 del contenido, invalidez automática | Bajo |
| multra-eu | **Repository + Row Mapper tipado** formal (eliminar normalización ad-hoc de keys camelCase del NeonAdapter — bug latente memorizado #142) | El fix actual de normalización de keys reconstruye mapeo por heurística; un mapper explícito por entidad lo vuelve determinista y cubierto por los 25 tests | Medio |
| **mymochis** | **Storage Strategy** (LocalDisk ↔ Cloudflare R2) tras una interfaz única | Pendiente migración R2 ya identificada (#130); Strategy evita reescribir routes/uploads.js | Bajo |
| mymochis | **Test baseline mínimo** (`node --test`, 0 deps, como multra-eu) | Único monolito en producción sin suite crítico | Bajo |
| **mymochis-studio** | **Tenant-as-Branch (silo logical)** + **Provisioning Factory** | ADR-005 ya decide Neon branch-per-tenant; formalizar Factory para creación/aplicación de template schema + **tenant resolver middleware** como port | Medio |
| **NX-Studio** | **Shared isomorphic pricing module** (single source) publicado para client (cotizador.astro) y server (api/licitacion) | Hoy hay engine espejo client↔server → drift garantizado; unificar en `src/data/pricing.ts` importado por ambos (Astro islands permite) | Bajo |
| **portafolio** | **Visual regression Playwright** (screenshots por breakpoint por theme) | 5 themes + componentes 3D/audio = superficie frágil sin tests; screenshot diff como gate | Bajo |
| **NexoAccManager** | Mantener **hexagonal**; **Elevar coverage gate 68%→80%** como check CI | Ya es el ejemplar del workspace (skills tdd/verification). Gate impide regresión | Trivial |
| **flag-edge / supply-radar / contract-guard** | **Seam de interfaces** (Go interfaces por servicio) + table-driven tests | CLIs zero-dep ya testean bien; interfaces aíslan OSV/adapters externos para tests sin red | Bajo |
| **synthetic-trader** | Ejecutar REFACTORING_MASTER_PLAN: **Strategy pattern para estrategias** + **Repository para storage** (SQLite/Parquet) | Plan propio del repo ya lo prescribe; alinear antes de fase paper/live (gates de Sharpe) | Medio |
| **e14-fraud-detector** | Mantener **PAUSED**; al retomar: promover scripts `verificar_*.py` a pytest suite | Convierte checks manuales en regression harness | Bajo |
| **nx-booking / nx-bzns / pcshield-launch** | **git init + PROJECT.md mínimo** antes de cualquier mejora | Sin VCS no hay rollback | Trivial |
| **triqui** | **Archivar/borrar** (duplicado exacto de Tic-Tac-Toe) | Elimina mantenimiento doble | Trivial |
| **nexocore / grani-usco / nva-demons** | **AC block en PROJECT.md + build/test gate script** | Next.js grandes sin tests; empezar por smoke e2e (Playwright) de rutas críticas | Bajo-Medio |
| **infra Go (dark-memory/dark-research/github-mcp)** | Sin cambios: RFCs + JSON specs + conformance ya son ejemplares. Monitorear divergencia fork↔upstream | Son forks/vendors que dependen de upstream | n/a |

### 4.3 Patrones GoF/arquitectónicos por categoría (para consulta)

- **Creational**: Factory (provisioning de tenants, uploads), Builder (specs/artifacts dark-memory).
- **Structural**: Adapter (DB dual, storage), Façade (routers Express unificados), Decorator (middleware auth/rate-limit).
- **Behavioral**: Strategy (pricing engine, temas portafolio, storage, estrategias trader), Observer/SSE (flag-edge, eventos), Template Method (pipelines de test/verify), Chain of Responsibility (validación zod + middlewares).
- **Arquitectura**: Hexagonal (NexoAccManager; referencia para multra-eu/mymochis), Multi-tenant silo/pool/bridge (mymochis-studio = silo-logical vía branches), Content-hash cache (multra-eu), Micro-frontend NO recomendado (todos los front son monolitos chicos).

---

## 5. Plan de acción priorizado

1. **Inmediato (30 min)**: git init en 4 repos sin VCS · archivar triqui · agregar bloque AC a PROJECT.md de los 6 proyectos "doc descriptiva".
2. **Corto (1-2h c/u)**: tests baseline mymochis + multra-eu mapper determinista · storage Strategy (R2) · pricing module unificado NX-Studio · visual regression portafolio.
3. **Medio**: spec formal (vibe_spec) para los 6-7 proyectos activos restantes ·提升了 mymochis-studio provisioning Factory + local docker smoke (pendiente #108).
4. **Estratégico** (de ADR-005/#106): victoria multi-tenant stack OSS; rollout Kanban/routing v3 (mem #141).

## 6. Referencias

- dark-memory specs: `spec_context` #1–#11
- dark-memory memorias: #142 (Multra-EU prod), #141 (routing v3), #130 (stack deploy), #125 (research patrones), #113 (Neon+tests), #108 (estado mymochis-studio), #106 (ADR-005), #66 (scope MyMochis), #50/#19 (verificación tools + NLI pending)
- Research runs: #10 (multi-tenant SaaS), #14 (hexagonal Node.js), web 2026-09-29 (repository pattern, dual-DB adapters)
- Skills vinculadas: content-hash-cache-pattern, tdd-workflow, retrospection loop (NexoAccManager – contagio positivo)
