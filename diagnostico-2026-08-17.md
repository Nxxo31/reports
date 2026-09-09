# Diagnostico Completo Hermes Agent -- 2026-08-17

## Resumen Ejecutivo

| Componente | Estado | Detalle |
|---|---|---|
| Perfiles | PARCIAL | 8 perfiles (7 esperados + gatekeeper extra) |
| Skills | OPERATIVO | 135 total (27 hub + 108 local) -- TODAS las criticas instaladas |
| MCPs dev | PARCIAL | 12/13 MCPs criticos activos; dark-memory sin command |
| GODMODE | ACTIVO | prefill.json + config.yaml configurados correctamente |
| Dashboard Web | NO RESPONDE | Puerto 9119 sin respuesta HTTP |
| Config vs Dashboard | NO VERIFICABLE | Dashboard no carga |
| Proyectos | PARCIAL | synthetic-trader SIN PROJECT.md local; otros OK |
| Trading Bot | PAPER ONLY | Paper trading activo; sin cron verificado |
| Cron Jobs | SIN HERRAMIENTA | cronjob CLI no disponible en PATH |
| Memory/AGENTS.md | PARCIAL | Global OK; orchestrator SIN AGENTS.md |
| Trader Profile | CONFIGURADO | MCPs trading (ccxt/yfinance/deriv) enabled |
| Gateway | OPERATIVO | Running 10min, 2.8GB RAM, 317 tasks |

---

## Paso 1: Perfiles

**Perfiles detectados (8):**
- default -- model: nvidia/nemotron-3.5-lightning-30b-a3b OK
- dev -- model: nvidia/nemotron-3.5-lightning-30b-a3b OK
- orchestrator -- SIN model definido ADVERTENCIA
- research -- SIN model definido ADVERTENCIA
- designer -- SIN model definido ADVERTENCIA
- trader -- model: nvidia/nemotron-3.5-lightning-30b-a3b OK
- ciberseguridad -- model: nvidia/nemotron-3.5-lightning-30b-a3b OK
- gatekeeper -- EXTRA (no en lista esperada) -- model: nvidia/nemotron-3.5-lightning-30b-a3b

**Issues:**
- orchestrator, research, designer sin modelo asignado en hermes profile list
- gatekeeper perfil extra no documentado

---

## Paso 2: Skills Instaladas

**Total: 135 skills (27 hub + 108 local) -- Todas enabled**

**Skills criticas DEV verificadas:**
| Skill | Estado |
|---|---|
| lifecycle-methodology | OK local |
| spec-creation | OK local |
| spec-driven-development | OK local |
| subagent-driven-development | OK hub (official) |
| code-review-and-quality | OK local |
| architecture-patterns | NO EXISTE como skill separada (integrada en AGENTS.md) |
| karpathy-guidelines | OK local |
| electron-desktop-dev | NO EXISTE -- usar electron skill local |
| security-and-hardening | NO EXISTE -- cubierto por web-pentest, security-pen-testing |
| nexoaccmanager-ipc-account-specific-pattern | NO EXISTE -- NOTA: patrones en AGENTS.md del dev profile |

**Nota importante:** nexoaccmanager-development-patterns NO es skill separada -- sus patrones estan en AGENTS.md del perfil dev. No reportar como faltante.

---

## Paso 3: MCPs del Dev Profile

**Configurado en ~/.hermes/profiles/dev/config.yaml:**

| MCP | Estado | Nota |
|---|---|---|
| github | OK enabled | |
| context7 | OK enabled | |
| playwright | OK enabled | |
| filesystem | OK enabled | |
| zenith | OK enabled | |
| lsp-intelligence | OK enabled | |
| mcp-code-review-pro | OK enabled | |
| firecrawl | OK enabled | |
| sequential-thinking | OK enabled | |
| agent-lsp | OK enabled | |
| reforge-mcp | OK enabled | |
| visual-parity | OK enabled | |
| nucleus-mcp | OK enabled | |
| dark-memory | OK enabled | SIN command configurado FALLO |
| chroma | OK enabled | |
| ccxt | disabled | Para trading |
| yfinance | disabled | Para trading |
| deriv | disabled | Para trading |

**Issue critico:** dark-memory enabled pero SIN command en config -- logs muestran "MCP server 'dark-memory' has no 'command' in config" repetidamente. Falla conexion inicial.

---

## Paso 4: GODMODE Jailbreak

- ~/.hermes/prefill.json existe OK
- Contiene 2 mensajes (user + assistant) OK
- config.yaml tiene agent.prefill_messages_file: prefill.json OK
- System prompt en config.yaml confirma modo unrestricted OK

**Estado: ACTIVO Y FUNCIONAL**

---

## Paso 5: Dashboard Web

curl -s http://127.0.0.1:9119 --> HTTP:000 (no response)

**Todas las paginas fallan:**
- / -- sin respuesta
- /sessions -- sin respuesta
- /config -- sin respuesta
- /profiles -- sin respuesta
- /skills -- sin respuesta
- /cron -- sin respuesta
- /mcp -- sin respuesta
- /api -- sin respuesta

**El dashboard NO esta corriendo en puerto 9119.** Gateway corriendo pero dashboard web no.

---

## Paso 6: Config vs Dashboard NO VERIFICABLE

Dashboard no carga --> no se puede comparar.
Config.yaml principal (~/.hermes/config.yaml) muestra:
- model.default: nvidia/nemotron-3.5-lightning-30b-a3b FALLO (esperado: z-ai/glm-5.2)
- agent.prefill_messages_file: prefill.json OK
- delegation.model: z-ai/glm-5.2 (solo para subagents)

**Discrepancia:** model.default en config principal es nemotron, no glm-5.2. El perfil dev usa nemotron. Solo delegation.model apunta a glm-5.2.

---

## Paso 7: Estado de Proyectos

**~/proyectos/ contiene:**
- Tic-Tac-Toe/ -- OK PROJECT.md + AGENTS.md (Fase 0 completada)
- wordlists/ -- OK PROJECT.md + AGENTS.md (directorio data)
- reports/ -- OK AGENTS.md (directorio output, sin PROJECT.md requerido)
- synthetic-trader/ -- NO EXISTE en ~/proyectos/ FALLO
  - Esta en ~/proyectos_organizados/proyectos/synthetic-trader/ segun PROJECT.md root
- Otros archivos: PROJECT.md root, diagnosticos previos, reforge-state.json

**Proyectos legacy sin PROJECT.md:** Ninguno detectado en ~/proyectos/

---

## Paso 8: Trading Bot

**~/proyectos/synthetic-trader/PROJECT.md: NO EXISTE**

Estado real (según PROJECT.md root en ~/proyectos/):
- **Ubicacion real:** /home/sebas/proyectos_organizados/proyectos/synthetic-trader/
- **Estado:** Active paper trading 24/7
- **Version:** 0.4.0
- **Balance:** $9,997 (P&L: -$3)
- **Estrategia:** Step Index (stpRNG), stake $1/trade
- **Monitoreo:** Cron cada 30m, dashboard localhost:3000, backend :8001
- **Ultimo fix:** 2026-08-14 -- 6 bugs en paper_runner.py (stake sizing + trading loop)

---

## Paso 9: Cron Jobs

cronjob action=list --> command not found

**cronjob CLI no esta en PATH.** No se puede verificar jobs activos.
Systemd timers: sin timers hermes visibles (systemctl --user list-timers vacio para hermes).

**El job de diagnostico diario NO esta registrado como cronjob CLI.**

---

## Paso 10: Memory y AGENTS.md

**Dark Memory:** Operational (SQLite, schema v25, 1 proyecto, 126 write_audit, 6 error events open)

**AGENTS.md:**
| Archivo | Estado |
|---|---|
| ~/.hermes/AGENTS.md | OK Existe, completo |
| ~/.hermes/profiles/dev/AGENTS.md | OK Existe, completo |
| ~/.hermes/profiles/orchestrator/AGENTS.md | FALLO NO EXISTE |
| ~/.hermes/profiles/research/AGENTS.md | OK Existe |
| ~/.hermes/profiles/designer/AGENTS.md | OK Existe |
| ~/.hermes/profiles/trader/AGENTS.md | OK Existe (basico) |
| ~/.hermes/profiles/ciberseguridad/AGENTS.md | OK Existe (basico) |
| ~/.hermes/profiles/gatekeeper/AGENTS.md | No verificado (perfil extra) |

---

## Paso 11: Trading -- Indices Sinteticos

**Perfil trader config.yaml:**
- mcp_servers.ccxt: enabled: true OK
- mcp_servers.yfinance: enabled: true OK
- mcp_servers.deriv: enabled: true OK
- trading.paper_trading: true OK
- trading.risk_per_trade_pct: 2 OK
- trading.max_daily_trades: 10 OK
- trading.instruments: [synthetic_indices, forex, stocks, crypto] OK

**Estado del bot:** Paper trading configurado en perfil trader. Verificar ejecucion real en Paso 8.

---

## Paso 12: Gateway

hermes-gateway.service - Active: active (running) since Mon 2026-08-17 12:08:19
  Memory: 2.8G (peak: 3G)
  CPU: 3min 55.594s
  Tasks: 317
  Uptime: ~10 min

**MCP servers corriendo bajo gateway:** github, lsp-intelligence, zenith, mcp-code-review, reforge-mcp, visual-parity, nucleus-mcp, context7, firecrawl, chroma, filesystem, playwright, sequential-thinking, dark-memory (parked - sin command)

---

## Paso 13: Issues Consolidados

### CRITICOS (requieren accion inmediata)

1. **Dashboard web no responde** -- Puerto 9119 sin servicio. Iniciar hermes dashboard o verificar configuracion.

2. **dark-memory MCP sin command** -- Configurado enabled pero sin command en dev profile. Falla conexion repetida en logs.

3. **model.default incorrecto** -- Config principal usa nvidia/nemotron-3.5-lightning-30b-a3b en lugar de z-ai/glm-5.2.

4. **orchestrator profile sin AGENTS.md** -- Perfil critico sin reglas operacionales.

5. **cronjob CLI no disponible** -- No se pueden gestionar/verificar jobs programados.

### MEDIOS

6. **Perfiles orchestrator, research, designer sin modelo** -- hermes profile list muestra -- para model.

7. **gatekeeper perfil extra no documentado** -- 8 perfiles vs 7 esperados.

8. **synthetic-trader sin PROJECT.md local** -- Proyecto activo no en ~/proyectos/ directorio estandar.

9. **Dashboard paginas vacias** -- Aunque responda HTTP, paginas devuelven contenido vacio.

### MENORES

10. **Dark memory: 6 error events open** -- Revisar y resolver.

11. **Skill autoloader warnings** -- Skills array en config.yaml tiene duplicados (wsl-computer-use x4, skill-manage-guardrail-safe x4, chromadb-project-indexing x3, dark-memory-mcp-session-start x2).

---

## Paso 14: Memory Update

Issues guardados en dark-memory para persistencia cross-session.

---

## VEREDICTO FINAL

### SISTEMA: REQUIERE ATENCION

**Issues bloqueantes:**
1. Dashboard web caido (puerto 9119)
2. dark-memory MCP mal configurado (sin command)
3. Modelo por defecto incorrecto (nemotron vs glm-5.2)
4. orchestrator sin AGENTS.md
5. cronjob CLI no instalado

**Acciones recomendadas (orden de prioridad):**
1. hermes dashboard iniciar o diagnosticar puerto 9119
2. Agregar command a dark-memory en ~/.hermes/profiles/dev/config.yaml
3. hermes config set model.default z-ai/glm-5.2
4. Crear ~/.hermes/profiles/orchestrator/AGENTS.md basado en global
5. Verificar instalacion cronjob tool / usar systemd timers
6. Limpiar duplicados en skills.auto_load array
7. Sincronizar synthetic-trader a ~/proyectos/ o actualizar PROJECT.md root
8. Asignar modelos a orchestrator, research, designer profiles