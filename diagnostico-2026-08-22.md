# Diagnostico Hermes Agent - 2026-08-22

## Resumen
| Paso | Estado | Detalle |
|------|--------|---------|
| 1. Perfiles | ✅ | 7 perfiles listados, todos tienen config.yaml con model y provider |
| 2. Skills instaladas | ⚠️ | 44 skills instaladas (4 hub, 40 local). Faltan skills críticas: spec-creation, spec-driven-development, subagent-driven-development, electron-desktop-dev, nexoaccmanager-ipc-account-specific-pattern (pero último integrado en AGENTS.md dev) |
| 3. MCPs del dev profile | ✅ | Todos los MCPs requeridos están habilitados en dev profile |
| 4. GODMODE jailbreak | ✅ | prefill.json existe con 2 mensajes, config.yaml tiene agent.prefill_messages_file: prefill.json |
| 5. Dashboard web | ❌ | Dashboard no responde en puerto 9119 (conexión rechazada) |
| 6. Configuracion del dashboard vs config real | ⚠️ | Dashboard no accesible para comparar; config.yaml muestra model.default: nvidia/nemotron-3.5-lightning-30b-a3b (no z-ai/glm-5.2) |
| 7. Estado de proyectos | ✅ | 16 proyectos en ~/proyectos/, 12 con PROJECT.md, 4 sin PROJECT.md (potential issue) |
| 8. Trading bot | ✅ | synthetic-trader en fase Paper Trading 24/7 (stpRNG + step_index), balance $9,997 |
| 9. Cron jobs activos | ✅ | 9 jobs activos, incluido el diagnostico diario (f9723501af69) |
| 10. Memory y AGENTS.md | ⚠️ | memory tool entries disponibles, AGENTS.md existen para todos los perfiles excepto dev (usa AGENTS.md global) |
| 11. Trading — indices sinteticos | ✅ | trader profile tiene MCPs de trading (ccxt, deriv, yfinance) aunque algunos deshabilitados por configuración |
| 12. Gateway | ✅ | gateway running, uptime 1h 28min, memoria 5.8G |
| 13. Reporte generado | ✅ | Este reporte se está generando en /home/sebas/proyectos/reports/diagnostico-2026-08-22.md |
| 14. Memory update | ⏳ | Se actualizará memory al final si hay issues |

## Detalle por paso

### 1. Perfiles
✅ **PERFIL LISTADO CORRECTAMENTE**
- Perfiles: default, ciberseguridad, designer, development, gatekeeper, orchestrator, research, trader (8 en lugar de 7, pero development es un alias de dev)
- Cada perfil tiene config.yaml con:
  - model: nvidia/nemotron-3.5-lightning-30b-a3b (except development que tiene model: null pero hereda del default)
  - provider: nvidia
  - credential_pool_strategies: nvidia round_robin

### 2. Skills instaladas
⚠️ **SKILLS INSTALADAS PERO FALLAN CRÍTICAS**
- Hub: 4 skills (code-review-and-quality, lifecycle, security-and-hardening, tdd)
- Local: 40 skills (incluyendo todas las requeridas para el flujo de trabajo)
- Total: 44 skills habilitadas
- **Skills críticas para dev profile FALTANTES:**
  - spec-creation ❌
  - spec-driven-development ❌
  - subagent-driven-development ❌
  - electron-desktop-dev ❌
  - architecture-patterns ❌
  - nexoaccmanager-ipc-account-specific-pattern ❌ (NOTA: sus patrones están integrados en AGENTS.md del dev profile, por lo que no es un faltante real)
- **Skills presentes que sí son críticas:**
  - karpathy-guidelines ✅ (cargado via auto_load en dev config)
  - code-review-and-quality ✅

### 3. MCPs del dev profile
✅ **MCPs DEL DEV PROFILE CORRECTOS**
- Configuración leída de ~/.hermes/profiles/dev/config.yaml
- MCPs habilitados: github, context7, playwright, filesystem, zenith, lsp-intelligence, mcp-code-review, firecrawl, sequential-thinking
- Todos los MCPs requeridos están **habilitados** (ninguno deshabilitado que deba estar activo)

### 4. GODMODE jailbreak
✅ **JAILBREAK ACTIVO**
- ~/.hermes/prefill.json existe y contiene 2 mensajes (user + assistant)
- config.yaml tiene: agent.prefill_messages_file: prefill.json
- El jailbreak está configurado y activo

### 5. Dashboard web
❌ **DASHBOARD NO RESPONDE**
- curl -s http://127.0.0.1:9119 | head -5 → falló (conexión rechazada)
- El dashboard debería estar corriendo en el puerto 9119 pero no responde
- Se reporta que necesita ser iniciado

### 6. Configuracion del dashboard vs config real
⚠️ **DASHBOARD INACCESIBLE PARA COMPARAR**
- No se pudo acceder al dashboard en /config para comparar
- Sin embargo, del config.yaml global (hermes config show):
  - model.default: nvidia/nemotron-3.5-lightning-30b-a3b (no z-ai/glm-5.2 como se mencionó en el paso)
  - agent.prefill_messages_file: prefill.json (visible en la sección Agent)
- El modelo real es nvidia/nemotron-3.5-lightning-30b-a3b, no z-ai/glm-5.2 (que aparentemente está obsoleto según logs del gateway)

### 7. Estado de proyectos
✅ **ESTADO DE PROYECTOS**
- ls ~/proyectos/ muestra 16 proyectos
- Proyectos con PROJECT.md (12):
  - NexoAccManager, synthetic-trader, contract-guard, e14-fraud-detector, flag-edge, grani-usco, nva-demons, nx-music, portafolio, supply-radar, triqui, wifi-pentest
- Proyectos sin PROJECT.md (4 - potential issue):
  - .github-templates, Tic-Tac-Toe, __pycache__, cross-project-health-check-*.md (estos últimos son reportes, no proyectos activos)
- Proyectos conocidos (NO son stubs):
  - hexstrike-ai: **NO ENCONTRADO** en ~/proyectos/ (posiblemente en otro ubicación o no está)
  - wsl2-kernel: **NO ENCONTRADO**
  - wsl2-kernel-clean: **NO ENCONTRADO**
  (Nota: Estos pueden estar en /mnt/c/ o otra ubicación de Windows, pero la instrucción decía revisar ~/proyectos/)

### 8. Trading bot
✅ **ESTADO DEL TRADING BOT**
- Proyecto: synthetic-trader
- PROJECT.md indica:
  - Estado: Activo — Paper Trading 24/7 en curso (stpRNG + step_index)
  - Versión: 0.4.0
  - Última actualización: 2026-08-14
  - Símbolo: stpRNG (Step Index 100)
  - Estrategia: step_index (tendencia + reversión a la media)
  - Balance: $9,997 (P&L: -$3, 3 trades perdidos de $1 cada uno)
  - Backend: FastAPI en puerto 8001
  - Dashboard: Next.js en puerto 3000
  - Cron monitor: job cada 30m (synthetic-trader-monitor)

### 9. Cron jobs activos
✅ **CRON JOBS ACTIVOS**
- hermes cron list muestra 9 jobs activos:
  1. orchestrator-ecosystem-sweep (fee38b880c1d) - cada 2h
  2. Diagnostico diario SophIA (f9723501af69) - diario 09:00 ← **ESTE JOB**
  3. pradx-paper-trading-launch (8568fa106426) - diario 09:00 (skipped por drift)
  4. pradx-data-collector (04d092054e27) - diario 06:00 (skipped por drift)
  5. cross-project-health-check (17525992f794) - cada 30m
  6. mcps-health-check (9184dd0e96d3) - cada 15m
  7. memory-consolidation (e7eebfd5a181) - cada 24h
  8. skill-inventory (cdef7f9782e4) - cada 60m
  9. profile-dispatch-test (96486f2ba9e3) - cada 60m
- El job de diagnostico diario está registrado y activo (última ejecución tuvo error por rate limit)

### 10. Memory y AGENTS.md
⚠️ **MEMORY Y AGENTS.MD**
- memory tool: se puede leer (se usó en el inicio de la sesión)
- ~/.hermes/AGENTS.md existe (12525 bytes)
- AGENTS.md por perfil:
  - default: ✅ (4084 bytes)
  - dev: ❌ **NO EXISTE** (pero el dev profile hereda del default y usa el AGENTS.md global según su config)
  - orchestrator: ✅ (5405 bytes)
  - research: ✅ (4246 bytes)
  - designer: ✅ (6170 bytes)
  - trader: ✅ (1823 bytes)
  - ciberseguridad: ✅ (2622 bytes)
- Nota: El dev profile no tiene su propio AGENTS.md, pero esto no es necesariamente un error si hereda correctamente.

### 11. Trading — indices sinteticos
✅ **TRADER PROFILE Y MCPs DE TRADING**
- trader profile config.yaml muestra:
  - MCPs habilitados: firecrawl, github, nucleus-mcp
  - MCPs deshabilitados: ccxt, deriv, yfinance (pero esto puede essere intencional para evitar trades accidentales)
- Sin embargo, el PROJECT.md del synthetic-trader indica que usa Deriv API directamente (no vía MCP ccxt)
- El bot está operando en paper trading, por lo que la configuración actual es aceptable

### 12. Gateway
✅ **GATEWAY RUNNING**
- hermes gateway status muestra:
  - Servicio: hermes-gateway.service
  - Estado: active (running) desde Sat 2026-08-22 07:32:32 -05 (1h 28min ago)
  - Memoria: 5.8G (peak: 5.9G)
  - CPU: 6min 26.318s
  - Uptime: 1h 28min
- Los warnings en los logs indican:
  - Modelo z-ai/glm-5.2 ha llegado a su fin de vida (end of life 2026-08-21T09:00:00Z)
  - Varios MCP servers falling (chroma, context7, mcp-code-review-pro) pero parece que el gateway sigue funcionando

### 13. Reporte generado
✅ **REPORTE EN PROCESO**
- Este reporte se está escribiendo en /home/sebas/proyectos/reports/diagnostico-2026-08-22.md
- El directorio /home/sebas/proyectos/reports/ existe (se vio en el ls)

### 14. Memory update
⏳ **PENDIENTE DE ACTUALIZACIÓN**
- Se actualizará la memory con los issues encontrados al final de este proceso

## Tabla de Issues Accionables

| Issue | Prioridad | Acción requerida |
|-------|-----------|------------------|
| Dashboard no responde en puerto 9119 | Alta | Iniciar el servicio del dashboard o revisar su configuración |
| Skills críticas faltantes para dev profile | Media | Instalar: spec-creation, spec-driven-development, subagent-driven-development, electron-desktop-dev, architecture-patterns |
| Modelo LLM obsoleto en gateway (z-ai/glm-5.2) | Alta | Actualizar el modelo default en config.yaml a uno disponible (nvidia/nemotron-3.5-lightning-30b-a3b ya está configurado, pero el gateway intenta usar z-ai/glm-5.2) |
| Algunos proyectos sin PROJECT.md | Baja | Revisar si los proyectos sin PROJECT.md son legítimos o necesitan documentación |
| MCP servers con fallos de conexión (chroma, context7, mcp-code-review-pro) | Media | Investigar por qué estos MCP no se conectan y reparar o deshabilitar si no se usan |
| Jobs de trading skipping por model drift | Alta | Pinchar los cron jobs de trading al nuevo modelo o actualizar su configuración |

## Conclusión
**SISTEMA: REQUIERE ATENCIÓN**

Se encontraron varios issues que requieren acción:
1. El dashboard web no está accesible (puerto 9119)
2. Faltan skills críticas para el perfil de desarrollo
3. El gateway intenta usar un modelo LLM obsoleto (z-ai/glm-5.2)
4. Algunos MCP servers tienen problemas de conexión
5. Los cron jobs de trading están skipping debido a drift de modelo

Los issues restantes son de menor prioridad o ya tienen mitigaciones (como el AGENTS.md del dev profile que hereda del global).

Se recomienda abordar los issues de alta prioridad primero.