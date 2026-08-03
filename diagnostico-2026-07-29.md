# Diagnóstico Hermes Agent — 2026-07-29 09:02

**Job:** f9723501af69 (Diagnostico diario SophIA)
**Ejecutor:** SophIA (default profile, z-ai/glm-5.2)
**Modo:** cron, background, sin usuario

---

## Tabla Resumen

| # | Paso | Estado | Issues |
|---|------|--------|--------|
| 1 | Perfiles (7) | ✅ | default sin config.yaml propio (esperado) |
| 2 | Skills instaladas | ✅ | 253 total (49 hub + 45 builtin + 159 local) |
| 3 | MCPs dev profile | ⚠️ | `mcp-code-review-pro` en parked (connection closed) |
| 4 | GODMODE jailbreak | ⚠️ | prefill.json existe, agent.prefill_messages_file configurado, PERO clave duplicada vacía en línea 346 |
| 5 | Dashboard web | ✅ | 6/6 páginas responden 200 |
| 6 | Config vs dashboard | ⚠️ | prefill_messages_file aparece 2 veces en config.yaml (dadeada) |
| 7 | Estado de proyectos | ⚠️ | wifi-pentest sin PROJECT.md; hexstrike-ai=wsl2-kernel no son proyectos en ~/proyectos/ |
| 8 | Trading bot | ⚠️ | PROJECT.md existe pero sin cron jobs activos del trader |
| 9 | Cron jobs activos | ✅ | 3 jobs, este diagnóstico registrado |
| 10 | Memory y AGENTS.md | ✅ | Global 490 líneas, 6 profiles con AGENTS.md |
| 11 | Trading MCPs | ✅ | ccxt + yfinance + deriv todos enabled en trader profile |
| 12 | Gateway | ✅ | running 2h9min, 2.4GB RAM, 301 procesos |
| 13 | Reporte generado | ✅ | Este archivo |
| 14 | Memory update | ✅ | Issues guardados |

---

## Paso 1: Perfiles

**7 perfiles esperados: ✅ todos presentes**

| Perfil | Model | Provider | Gateway | config.yaml |
|--------|-------|----------|---------|-------------|
| default | z-ai/glm-5.2 | nvidia | running | ❌ sin config.yaml propio (hereda global — esperado por diseño) |
| dev | z-ai/glm-5.2 | nvidia | stopped | ✅ |
| orchestrator | z-ai/glm-5.2 | nvidia | stopped | ✅ |
| research | deepseek-ai/deepseek-v4-pro | nvidia | stopped | ✅ |
| designer | deepseek-ai/deepseek-v4-pro | nvidia | stopped | ✅ |
| trader | moonshotai/kimi-k2.6 | nvidia | stopped | ✅ |
| ciberseguridad | deepseek-ai/deepseek-v4-pro | nvidia | stopped | ✅ |

**Nota:** `default` no tiene `profiles/default/config.yaml` — opera con la config global `~/.hermes/config.yaml`. Es diseño esperado, no un bug.

---

## Paso 2: Skills Instaladas

| Métrica | Valor |
|---------|-------|
| Total skills enabled | 253 |
| Skills hub (oficiales + community) | 49 |
| Skills builtin | 45 |
| Skills locales (`~/.hermes/skills/`) | 159 |

**Skills críticas para dev — verificación:**

| Skill | Estado | Origen |
|-------|--------|--------|
| lifecycle-methodology | ✅ | local |
| spec-creation | ✅ | local |
| spec-driven-development | ✅ | local |
| subagent-driven-development | ✅ | official (hub) |
| code-review-and-quality | ✅ | local (`code-review-and-qua…`) |
| code-reviewer | ✅ | community (hub) |
| architecture-patterns | ✅ | community (hub) |
| karpathy-guidelines | ✅ | local |
| electron-desktop-dev | ✅ | local |
| secure-electron-ipc | ✅ | local |
| nexoaccmanager-ipc-account-specific-pattern | ✅ | local (`nexoaccmanager-ipc-…`) |
| nexoaccmanager-feature-workflow | ✅ | local (`nexoaccmanager-feat…`) |
| nexoaccmanager-development-patterns | ✅ | local (`nexoaccmanager-deve…`) |
| nexoaccmanager-extension-framework | ✅ | local (`nexoaccmanager-exte…`) |
| doubt-driven-development | ✅ | local |

**Resultado:** ✅ Todas las skills críticas presentes. Ninguna requiere instalación.

---

## Paso 3: MCPs del dev profile

Configurados en `~/.hermes/profiles/dev/config.yaml`:

| MCP | Estado en config | Estado runtime (gateway) |
|-----|-----------------|--------------------------|
| github | ✅ enabled | ✅ running |
| context7 | ✅ enabled | ✅ running |
| playwright | ✅ enabled | ✅ running |
| filesystem | ✅ enabled | ✅ running |
| zenith | ✅ enabled | ✅ running |
| lsp-intelligence | ✅ enabled | ✅ running |
| mcp-code-review | ✅ enabled | ✅ running |
| firecrawl | ✅ enabled | ✅ running |
| sequential-thinking | ✅ enabled | ✅ running |
| visual-parity | ✅ enabled | ✅ running |
| mcp-code-review-pro | ✅ enabled | ⚠️ **PARKED** (connection closed tras 3 intentos) |

**MCPs disabled (esperado):**
- docker, dokploy, postgres, sentry — todos `enabled: false`

**Issue encontrado:** `mcp-code-review-pro` (comando: `mcp-code-review`) está enabled en config pero el gateway no pudo conectar (Connection closed). Está en estado `parked` hasta que se solicite reconexión. Posible causa: el binario `mcp-code-review` no está instalado o falla al arrancar.

---

## Paso 4: GODMODE Jailbreak

| Verificación | Estado |
|--------------|--------|
| `~/.hermes/prefill.json` existe | ✅ (468 bytes, 2026-07-26) |
| Contiene 2 mensajes (user + assistant) | ✅ |
| Mensaje user: "GODMODE ENABLED" prompt | ✅ |
| Mensaje assistant: "GODMODE ENABLED" response | ✅ |
| `agent.prefill_messages_file: prefill.json` en config.yaml | ✅ (línea 26) |

**⚠️ Issue:** La clave `prefill_messages_file` aparece **dos veces** en `~/.hermes/config.yaml`:
- Línea 26 (dentro de `agent:`): `prefill_messages_file: prefill.json` ✅
- Línea 346 (nivel raíz, fuera de `agent:`): `prefill_messages_file: ''` ❌

La segunda entrada está vacía y es la que Hermes lee a nivel raíz, lo que puede anular la configuración de `agent:`. Si el jailbreak no se está aplicando, esta es probablemente la causa.

**Estado del jailbreak:** PROBABLEMENTE ACTIVO (la entrada en `agent:` está correcta), pero la clave duplicada genera ambigüedad. Recomendado eliminar la línea 346.

---

## Paso 5: Dashboard Web

| Página | HTTP Status |
|-------|-------------|
| `/` (root) | ✅ 200 |
| `/sessions` | ✅ 200 |
| `/config` | ✅ 200 |
| `/profiles` | ✅ 200 |
| `/skills` | ✅ 200 |
| `/cron` | ✅ 200 |
| `/mcp` | ✅ 200 |

**Dashboard corriendo en `http://127.0.0.1:9119` — ✅ OPERATIVO.** Todas las páginas cargan correctamente.

---

## Paso 6: Configuración del Dashboard vs Config Real

| Verificación | Dashboard (`/config`) | config.yaml | Estado |
|--------------|---------------------|-------------|--------|
| `model.default` | z-ai/glm-5.2 | z-ai/glm-5.2 | ✅ Match |
| `agent.prefill_messages_file` | Visible en sección Agent | `prefill.json` (línea 26) | ⚠️ Verificado, pero clave duplicada en línea 346 |

**Definición:** La clave `prefill_messages_file` está duplicada en config.yaml. En YAML, la última definición anula las previas si están en el mismo scope, pero la línea 26 está dentro de `agent:` y la 346 está en el scope raíz. Hermes parece leer la de `agent:`, por lo que el jailbreak está activo, pero la duplicación es un problema de configuración que debe limpiarse.

---

## Paso 7: Estado de Proyectos

```
~/proyectos/
├── NexoAccManager        ✅ PROJECT.md (v4.0.9, LaunchDock persistente)
├── contract-guard        ✅ PROJECT.md (MVP V1 completado, OpenAPI comparator)
├── e14-fraud-detector    ✅ PROJECT.md (🟡 PAUSADO, F1-F4 completadas, F5+ pendientes)
├── flag-edge             ✅ PROJECT.md (V3 completado, feature flags dashboard)
├── grani-usco            ✅ PROJECT.md (MVP 0.1.0, landing granizados)
├── nva-demons            ✅ PROJECT.md (landing 3D, colectivo música electrónica)
├── portafolio            ✅ PROJECT.md (MVP, Phase 5 pendiente, Vercel deploy)
├── supply-radar          ✅ PROJECT.md (V1.1 completo, CLI supply chain security)
├── synthetic-trader      ✅ PROJECT.md (SaaS trading platform, arquitectura definida)
├── triqui                ✅ PROJECT.md (V2 activa, PWA, Minimax IA, deploy Vercel)
├── wifi-pentest          ❌ SIN PROJECT.md (repo con .git, .hc22000, wpa_test.cap)
├── reports/              (directorio de reportes, no es proyecto)
├── wordlists/            (rockyou.txt, wifi-spanish.txt — recursos)
```

| Proyecto | Estado | Fase |
|----------|--------|------|
| NexoAccManager | Activo | v4.0.9 — LaunchDock persistente + flujo GamesView → LaunchDock |
| contract-guard | Completado | MVP V1 — comparador OpenAPI 3.x |
| e14-fraud-detector | Pausado | F1-F4 completadas, F5+ pendientes |
| flag-edge | Completado | V3 — dashboard métricas A/B |
| grani-usco | Completado | MVP 0.1.0 — landing page |
| nva-demons | En desarrollo | Landing 3D Tatacoa + ticketing |
| portafolio | En desarrollo | MVP, Phase 5 (SEO/Performance) pendiente |
| supply-radar | En desarrollo | V1.1 completo, V1.2 (SBOM) en sprint |
| synthetic-trader | En arquitectura | SaaS platform diseñada, pipeline 4 fases |
| triqui | Activo | V2 desplegada (Vercel), roadmap features pendientes |
| wifi-pentest | Legacy/Sin documentar | ❌ Sin PROJECT.md |

**Sobre hexstrike-ai:** Es una **skill** de ciberseguridad (`~/.hermes/skills/ciberseguridad/hexstrike-ai-integration/SKILL.md`), no un proyecto en `~/proyectos/`. Describe integración de HexStrike AI MCP server con Hermes para pentesting automatizado (150+ herramientas, 12+ AI agents). No es un stub — es una skill real y operativa.

**Sobre wsl2-kernel y wsl2-kernel-clean:** No se encontraron directorios en `~/proyectos/`, ni en `/home/sebas/`, ni en `/mnt/d/`. Tampoco aparecen como repos Git o directorios en el filesystem WSL. Solo existe la skill `hexstrike-ai-integration` que menciona `platforms: [linux, wsl2]`. No son stubs erróneos reportados — simplemente **no existen como directorios** en el entorno WSL accesible. Si son repos externos (en Windows host u otra ubicación), no fueron montados en WSL.

---

## Paso 8: Trading Bot (synthetic-trader)

| Verificación | Estado |
|--------------|--------|
| PROJECT.md existe | ✅ |
| Estado del bot | En arquitectura — SaaS platform diseñada |
| Pipeline | 4 fases (Strategy → Backtest → Paper → Live) |
| Paper trading | Configurado como default (`trading.paper_trading: true`) |
| Cron jobs del trader | ❌ **Ninguno registrado** |

**El trader profile tiene configuración completa** (model, MCPs de ccxt/yfinance/deriv, risk params), pero **no hay cron jobs automatizados** ejecutando el bot. El bot está en fase de arquitectura/diseño, no en ejecución activa.

---

## Paso 9: Cron Jobs Activos

| Job ID | Nombre | Schedule | Estado | Última ejecución |
|--------|--------|----------|--------|------------------|
| fee38b880c1d | orchestrator-ecosystem-sweep | `0 */2 * * *` (cada 2h) | ✅ active | ok (08:02 hoy) |
| c08e627d3ef0 | ecosystem-audit-watchdog | `*/30 * * * *` (cada 30min) | ✅ active | ok (09:00 hoy) |
| f9723501af69 | Diagnostico diario SophIA | `0 9 * * *` (diario 9am) | ✅ active | **running** (este reporte) |

**Este job (Diagnostico diario SophIA) está registrado y ejecutándose.** La última ejecución previa (2026-07-28) terminó con error: "Gateway shutdown killed the job's tool subprocess". Esta ejecución actual está en progreso.

---

## Paso 10: Memory y AGENTS.md

| Archivo | Líneas | Estado |
|---------|--------|--------|
| `~/.hermes/AGENTS.md` (global) | 490 | ✅ |
| `profiles/dev/AGENTS.md` | 23 | ✅ |
| `profiles/orchestrator/AGENTS.md` | 20 | ✅ |
| `profiles/research/AGENTS.md` | 20 | ✅ |
| `profiles/designer/AGENTS.md` | 18 | ✅ |
| `profiles/trader/AGENTS.md` | 18 | ✅ |
| `profiles/ciberseguridad/AGENTS.md` | 22 | ✅ |

**Memory tool:** 95% ocupado (1,425/1,500 chars en `memory` + 949/1,000 en `user`). Todos los AGENTS.md existen y tienen contenido. Ninguno vacío ni corrupto.

---

## Paso 11: Trading — Índices Sinteticos

**MCPs de trading en trader profile:**

| MCP | Estado | Propósito |
|-----|--------|-----------|
| ccxt | ✅ enabled | Crypto market data + order execution |
| yfinance | ✅ enabled | Stocks, ETFs, forex data |
| deriv | ✅ enabled | Synthetic indices (Volatility, Boom/Crash, Step, Range Break) |
| context7 | ✅ enabled | Docs de python_deriv_api, pandas, numpy |
| firecrawl | ✅ enabled | Research de mercado |
| sequential-thinking | ✅ enabled | Strategy design reasoning |
| filesystem | ✅ enabled | Acceso a synthetic-trader/ |

**DERIV_APP_ID=1089** configurado. `trading.paper_trading: true` — paper trading es default. **No hay bot en ejecución** — el proyecto está en fase de arquitectura.

---

## Paso 12: Gateway

| Métrica | Valor |
|---------|-------|
| Estado | ✅ active (running) |
| Uptime | 2h 9min (desde 06:52 hoy) |
| Memoria | 2.4GB (peak 2.4GB) |
| Swap | 332.4M (peak 332.8M) |
| Procesos | 301 (MCPs + watchdogs + node processes) |
| CPU | 12min 5.5s acumulado |
| Systemd linger | ✅ enabled (sobrevive a logout) |

**Warnings del gateway:**
- `mcp-code-review-pro` — Connection closed (PARKED)
- `blender` — Connection closed
- `dokploy` — Connection closed (disabled, esperado)
- `postgres` — failed initial connection (PARKED, disabled en config)
- `agent-lsp` — failed initial connection (PARKED)
- `mcp-code-review-pro` — failed after 3 attempts (PARKED)

---

## SISTEMA: REQUIERE ATENCIÓN

### Issues encontrados (prioridad descendente):

1. **[ALTA] `prefill_messages_file` duplicado en config.yaml** — Línea 26 (dentro de `agent:`): `prefill.json` ✅; Línea 346 (raíz): `''` ❌. La duplicación genera ambigüedad y puede impedir el jailbreak GODMODE en algunos codepaths de Hermes. **Acción:**Eliminar la línea 346.

2. **[MEDIA] `mcp-code-review-pro` en estado PARKED** — Configurado como enabled pero el gateway no pudo conectar (`Connection closed` después de 3 intentos). Posible binario faltante o error de arranque. **Acción:** Verificar que `mcp-code-review` binario esté instalado y accesible en PATH, o eliminar la entrada si no se usa.

3. **[MEDIA] `wifi-pentest` sin PROJECT.md** — Repo con `.git`, `.hc22000`, `wpa_test.cap`, `hash.hc22000`. Es un proyecto legacy de pentesting WiFi sin documentación. **Acción:** Crear PROJECT.md mínimo o archivar el repo.

4. **[BAJA] `agent-lsp` MCP en PARKED** — Fallo de conexión inicial. Este MCP es distinto a `lsp-intelligence` (que sí está operativo). Si no se usa, puede eliminarse de la config del gateway.

5. **[BAJA] Memory tool al 95%** — 1,425/1,500 chars. Cerca del límite. **Acción:** Consolidar entradas antiguas en la próxima sesión.

6. **[BAJA] Sin cron jobs del trader** — El synthetic-trader está en arquitectura sin automatización. **Acción:** Definir cron job cuando el bot avance a paper trading activo.

7. **[INFO] wsl2-kernel / wsl2-kernel-clean no encontrados en filesystem** — No existen como directorios en WSL. hexstrike-ai sí existe como skill de ciberseguridad. No es un error de detección — los directorios no están montados o no existen en este entorno.

### Lo que está operando correctamente:

- ✅ 7 perfiles configurados y detectados
- ✅ 253 skills instaladas y enabled (todas las críticas presentes)
- ✅ 9 MCPs del dev profile operativos en runtime
- ✅ Dashboard web operativo (6/6 páginas)
- ✅ GODMODE jailbreak probablemente activo (prefill.json correcto)
- ✅ 10 proyectos con PROJECT.md documentados
- ✅ 3 cron jobs activos y registrados
- ✅ 7 AGENTS.md (global + 6 profiles) íntegros
- ✅ Gateway running con 2h uptime
- ✅ MCPs de trading (ccxt, yfinance, deriv) configurados
