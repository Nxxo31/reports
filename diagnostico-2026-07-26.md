# Diagnóstico Hermes Agent — 2026-07-26 09:00

**Job:** f9723501af69 (Diagnostico diario SophIA)
**Ejecutor:** SophIA (default profile, z-ai/glm-5.2)
**Modo:** cron, background, sin usuario

---

## Paso 1: Perfiles

7 perfiles esperados: ✅ todos presentes

| Perfil | Model | Provider | config.yaml | Estado |
|--------|-------|----------|-------------|--------|
| default | z-ai/glm-5.2 | nvidia | ❌ sin config.yaml (usa global) | running |
| dev | z-ai/glm-5.2 | nvidia | ✅ | stopped |
| orchestrator | z-ai/glm-5.2 | nvidia | ✅ | stopped |
| research | deepseek-ai/deepseek-v4-pro | nvidia | ✅ | stopped |
| designer | deepseek-ai/deepseek-v4-pro | nvidia | ✅ | stopped |
| trader | moonshotai/kimi-k2.6 | nvidia | ✅ | stopped |
| ciberseguridad | deepseek-ai/deepseek-v4-pro | nvidia | ✅ | stopped |

**Nota:** `default` no tiene `profiles/default/config.yaml` — opera con la config global `~/.hermes/config.yaml`. No es un bug, es el diseño esperado (el profile default hereda la config raíz).

---

## Paso 2: Skills Instaladas

| Métrica | Valor |
|---------|-------|
| Total skills instaladas | 177 (enabled) |
| Skills hub (oficiales + community) | 40 |
| Skills oficiales (hub) | 18 |
| Skills community (hub) | 18 (22 contando clawhub/skills.sh trusted) |
| Skills locales (`~/.hermes/skills/`) | 117 |

**Skills críticas para dev:**

| Skill | Estado | Origen |
|-------|--------|--------|
| lifecycle-methodology | ✅ | local |
| spec-creation | ✅ | local |
| spec-driven-development | ✅ | local |
| subagent-driven-development | ✅ | official (hub) |
| code-review-and-quality | ✅ | local |
| architecture-patterns | ✅ | local |
| karpathy-guidelines | ✅ | local |
| electron-desktop-dev | ✅ | local |
| security-and-hardening | ✅ | local |
| nexoaccmanager-development-patterns | ❌ NO EXISTE | — |
| nexoaccmanager-ipc-account-specific-pattern | ✅ | local |

**Issue detectado:** `nexoaccmanager-development-patterns` no se encuentra en `~/.hermes/skills/` ni en hub. Solo existe `nexoaccmanager-ipc-account-specific-pattern`. La skill de development patterns para NexoAccManager falta o fue renombrada. No se puede instalar vía `hermes skills install` porque no está publicada en el hub — es una skill local custom que debe crearse o restaurarse desde un backup.

---

## Paso 3: MCPs del Dev Profile

Config leída: `~/.hermes/profiles/dev/config.yaml`

| MCP | Estado |
|-----|--------|
| github | ✅ enabled |
| context7 | ✅ enabled |
| playwright | ✅ enabled |
| filesystem | ✅ enabled (`/home/sebas/proyectos`) |
| zenith | ✅ enabled (tools filtradas) |
| lsp-intelligence | ✅ enabled (tools filtradas) |
| mcp-code-review | ✅ enabled |
| firecrawl | ✅ enabled |
| sequential-thinking | ✅ enabled |
| visual-parity | ✅ enabled (extra) |
| docker | ✅ enabled (extra) |

Todos los MCPs críticos activos. Sin blockers.

---

## Paso 4: Estado de Proyectos

17 proyectos en `~/proyectos/`. Proyectos con PROJECT.md válido:

| Proyecto | Estado | Fase Actual |
|----------|--------|-------------|
| NexoAccManager | v4.0.6, activo | DT-6 SettingsView SRP refactor completado |
| contract-guard | MVP V1 completado | Push a GitHub |
| e14-audit-platform | En desarrollo | F1 completo, F2 en progreso (10 fases) |
| e14-auditoria | En desarrollo, v0.2.0 | Capa de ingestión implementada |
| flag-edge | V3 completado | Dashboard A/B + estadístico |
| grani-usco | MVP, v0.1.0 | Landing page funcional |
| hexstrike-ai | Stub generado | ecosweep auto-generated (sin PROJECT.md real) |
| nva-demons | En diseño | Landing 3D Tatacoa + ticketing |
| portafolio | MVP implementado | Phase 5 pendiente (SEO + deploy) |
| supply-radar | MVP V1.1 completado | Sprint V1.2 (SBOM export) |
| synthetic-trader | En arquitectura | SaaS platform (ver Paso 5) |
| triqui | V2 activa | PWA deployed, roadmap features pendientes |
| wsl2-kernel | Stub generado | ecosweep auto-generated |
| wsl2-kernel-clean | Stub generado | ecosweep auto-generated |

**Proyectos sin PROJECT.md (legacy):**
- `wifi-pentest` — sin PROJECT.md

**Proyectos con PROJECT.md stub (auto-generados por ecosweep, requieren contenido real):**
- `hexstrike-ai`
- `wsl2-kernel`
- `wsl2-kernel-clean`

---

## Paso 5: Trading Bot (Synthetic Trader)

**PROJECT.md leído:** SaaS platform para múltiples bots en Deriv.

| Métrica | Valor |
|---------|-------|
| Repositorio | `~/proyectos/synthetic-trader/` |
| Stack | Next.js 15 + FastAPI + PostgreSQL/TimescaleDB + Redis |
| Broker | Deriv (WebSocket API) |
| Estado | En arquitectura — sin fase de pipeline activa |
| Backtest | ⬜ No iniciado |
| Paper trading | ⬜ No iniciado |
| Live trading | ⬜ No iniciado (hard gate: requiere aprobación explícita) |
| Cron jobs del trader | ❌ Ninguno activo |
| Último trade | N/A |
| P&L | N/A |

**Trader MCPs (config.yaml):**
| MCP | Estado |
|-----|--------|
| ccxt | ✅ configurado |
| yfinance | ✅ configurado |
| deriv | ✅ configurado (requiere `DERIV_API_TOKEN` env) |
| context7 | ✅ |
| firecrawl | ✅ |
| sequential-thinking | ✅ |
| filesystem | ✅ |

**Issue:** el bot está en fase de arquitectura/diseño. No hay cron jobs del trader activos. No hay trades registrados. El directorio `strategies/` existe pero no se verificó contenido.

---

## Paso 6: Cron Jobs Activos

| ID | Nombre | Schedule | Estado | Última ejecución |
|----|--------|----------|--------|------------------|
| fee38b880c1d | orchestrator-ecosystem-sweep | `0 */2 * * *` (cada 2h) | ✅ active | 2026-07-26 02:04 ok |
| c08e627d3ef0 | ecosystem-audit-watchdog | `*/30 * * * *` (cada 30min) | ✅ active | 2026-07-26 02:01 ok |
| f9723501af69 | Diagnostico diario SophIA | `0 9 * * *` (diario 9am) | ✅ active (ejecutándose ahora) | 2026-07-26 09:00 running |

✅ Este job (diagnóstico diario) está registrado y activo.

---

## Paso 7: Memory y AGENTS.md

| Archivo | Tamaño | Estado |
|---------|--------|--------|
| `~/.hermes/AGENTS.md` | 23,029 bytes | ✅ Contenido SophIA completo |
| `~/.hermes/profiles/dev/AGENTS.md` | 26,008 bytes | ✅ Dev profile completo (8 fases) |
| `~/.hermes/profiles/orchestrator/AGENTS.md` | 15,773 bytes | ✅ Orchestrator profile completo (7 fases) |
| `~/.hermes/profiles/research/AGENTS.md` | ✅ | Presente |
| `~/.hermes/profiles/designer/AGENTS.md` | ✅ | Presente |
| `~/.hermes/profiles/trader/AGENTS.md` | ✅ | Presente |
| `~/.hermes/profiles/ciberseguridad/AGENTS.md` | ✅ | Presente |

**Memory tool:** 629/1500 chars (41% used). Entradas activas sobre profiles, GODMODE, skills oficiales, diagnóstico cron.

Ningún AGENTS.md vacío o corrupto.

---

## Paso 8: Trading — Índices Sintéticos

Cubierto en Paso 5. Resumen:
- Bot en fase arquitectura, sin pipeline activo
- MCPs de trading (ccxt, deriv, yfinance) configurados correctamente
- No hay cron jobs del trader
- `DERIV_API_TOKEN` debe estar en env vars (no verificado en este diagnóstico por seguridad)

---

## Paso 9: Resumen Final

### Checklist

| Paso | Estado |
|------|--------|
| 1. Perfiles (7/7) | ✅ |
| 2. Skills (10/11 críticas presentes) | ⚠️ 1 faltante |
| 3. MCPs dev (9/9 críticos) | ✅ |
| 4. Proyectos (13 con PROJECT.md, 1 legacy, 3 stubs) | ⚠️ 3 stubs |
| 5. Trading bot (en arquitectura) | ⚠️ Sin pipeline activo |
| 6. Cron jobs (3 activos, este job confirmado) | ✅ |
| 7. AGENTS.md (todos presentes) | ✅ |
| 8. Trading MCPs (configurados) | ✅ |

---

## SISTEMA: OPERATIVO (con 2 issues menores)

### Issues detectados:

1. **Skill faltante:** `nexoaccmanager-development-patterns` no existe en `~/.hermes/skills/` ni en el hub. Solo existe `nexoaccmanager-ipc-account-specific-pattern`. La skill de development patterns debe ser creada o restaurada desde backup. No bloquea desarrollo pero el dev AGENTS.md la referencia como obligatoria.

2. **Proyectos stub:** `hexstrike-ai`, `wsl2-kernel`, `wsl2-kernel-clean` tienen PROJECT.md auto-generados por ecosweep sin contenido real. Requieren documentación manual o eliminación si no son proyectos activos.

### No-issues (confirmados sanos):

- Todos los MCPs críticos del dev profile están enabled y configurados
- Los 7 perfiles existen con model + provider correctos
- Los 3 cron jobs activos están funcionando (última ejecución OK)
- AGENTS.md global + todos los profiles están presentes y completos
- Memory tool operativa al 41% de capacidad

### Acciones recomendadas:

1. Crear o restaurar `~/.hermes/skills/nexoaccmanager-development-patterns/` con el contenido de patrones de desarrollo del proyecto
2. Documentar o eliminar los 3 proyectos stub de ecosweep
3. (Opcional) Activar cron jobs del trader cuando el synthetic-trader avance a paper trading
