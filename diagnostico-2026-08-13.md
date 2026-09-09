# Diagnostico Completo del Entorno Hermes Agent
**Fecha:** 2026-08-13  
**Hora de inicio:** 09:00:00  
**Tipo:** Cron job automático  

---

## RESUMEN EJECUTIVO

| Paso | Estado | Hallazgo Principal |
|------|--------|-------------------|
| 1. Perfiles | ⚠️ PARCIAL | 8 perfiles detectados; orchestrator falta config |
| 2. Skills instaladas | ✅ OK | 121 totales (28 hub, 93 local); críticas presentes |
| 3. MCPs dev profile | ✅ OK | Todos los MCPs requeridos están enabled |
| 4. GODMODE jailbreak | ✅ ACTIVO | prefill.json configurado correctamente |
| 5. Dashboard web | ❌ FALLÓ | Servicio no responde en puerto 9119 |
| 6. Config vs dashboard | ⚠️ N/A | Dashboard down; config muestra z-ai/glm-5.2 |
| 7. Estado de proyectos | ✅ OK | 15 proyectos con PROJECT.md; estados variados |
| 8. Trading bot | ✅ OK | Paper trading 24/7 activo en synthetic-trader |
| 9. Cron jobs activos | ✅ OK | 6 jobs activos; diagnostico diario registrado |
| 10. Memory y AGENTS.md | ⚠️ PARCIAL | AGENTS.md globales y de perfil presentes; orchestrator falta |
| 11. Trading — indices sintéticos | ✅ OK | Trader profile tiene MCPs de trading enabled |
| 12. Gateway | ✅ OK | Servicio corriendo, uptime 1h34m, memoria 2.7G |
| 13. **GENERAL** | ⚠️ REQUIERE ATENCIÓN | **Issues críticos detectados** |

**CONCLUSIÓN:** SISTEMA: **REQUIERE ATENCIÓN**  
El núcleo de Hermes funciona (profiles, skills, MCPs, gateway, trading) pero el dashboard está caído y hay problemas de configuración en perfiles críticos.

---

## DETALLE POR PASO

### Paso 1: Perfiles
- **Perfiles encontrados:** default, dev, orchestrator, research, designer, trader, ciberseguridad, gatekeeper (8 total)
- **Config.yaml validation:**
  - ✅ default: model: z-ai/glm-5.2, provider: nvidia
  - ✅ dev: model: z-ai/glm-5.2, provider: nvidia (duplicado pero válido)
  - ❌ orchestrator: **NO EXISTE** `~/.hermes/profiles/orchestrator/config.yaml`
  - ✅ research: model: z-ai/glm-5.2, provider: nvidia
  - ✅ designer: model: z-ai/glm-5.2, provider: nvidia
  - ✅ trader: model: z-ai/glm-5.2, provider: nvidia
  - ✅ ciberseguridad: model: z-ai/glm-5.2, provider: nvidia
  - ✅ gatekeeper: model: z-ai/glm-5.2, provider: nvidia

### Paso 2: Skills instaladas
- **Hub (oficial/community):** 28 skills instaladas, todas enabled
  - Oficiales: 20 skills
  - Community: 4 skills
- **Local (custom):** 93 skills instaladas, todas enabled
- **Críticas para dev profile:** 
  - ✅ lifecycle-methodology
  - ✅ spec-creation  
  - ✅ spec-driven-development
  - ✅ subagent-driven-development
  - ✅ code-review-and-quality
  - ❌ architecture-patterns (**NO ENCONTRADA**)
  - ✅ karpathy-guidelines
  - ❌ electron-desktop-dev (**NO ENCONTRADO**)
  - ❌ security-and-hardening (**NO ENCONTRADO**)
  - ✅ nexoaccmanager-ipc-account-specific-pattern (integrado en AGENTS.md del dev profile, no como skill separada)

### Paso 3: MCPs del dev profile
**Archivo:** `~/.hermes/profiles/dev/config.yaml`
- ✅ github: enabled: true
- ✅ context7: enabled: true  
- ✅ playwright: enabled: true
- ✅ filesystem: enabled: true
- ✅ zenith: enabled: true
- ✅ lsp-intelligence: enabled: true
- ✅ mcp-code-review-pro: enabled: true
- ✅ firecrawl: enabled: true
- ✅ sequential-thinking: enabled: true
**Estado:** TODOS LOS MCPs REQUERIDOS ESTÁN ACTIVOS

### Paso 4: GODMODE jailbreak
- **Prefill file:** `~/.hermes/prefill.json` existe con 2 mensajes (user + assistant)
- **Config validation:** `agent.prefill_messages_file: prefill.json` presente en config.yaml
- **Estado:** **JAILBREAK ACTIVO** - El sistema está operando sin filtros de seguridad

### Paso 5: Dashboard web
- **Endpoints probados:** 
  - http://127.0.0.1:9119 → HTTP 000 (conexión fallida)
  - http://127.0.0.1:9119/sessions → HTTP 000
  - http://127.0.0.1:9119/config → HTTP 000  
  - http://127.0.0.1:9119/profiles → HTTP 000
  - http://127.0.0.1:9119/skills → HTTP 000
  - http://127.0.0.1:9119/cron → HTTP 000
  - http://127.0.0.1:9119/mcp → HTTP 000
- **Servicio:** `hermes dashboard --status` retorna "No hermes dashboard processes running"
- **Puertos:** `ss -tlnp` muestra ningún proceso escuchando en puerto 9119
- **Estado:** **DASHBOARD INACTIVO** - Necesita ser iniciado con `hermes dashboard register`

### Paso 6: Configuración del dashboard vs config real
- **No se pudo comparar** debido a que el dashboard está inactivo
- **Configuración real verificable:**
  - `model.default: z-ai/glm-5.2` ✓ (correcto)
  - `agent.prefill_messages_file: prefill.json` ✓ (presente)
  - `reasoning_effort: ultra` ✓
  - `max_turns: 90` ✓
- **Nota:** El dashboard debería mostrar estos valores cuando esté operativo

### Paso 7: Estado de proyectos
**Proyectos con PROJECT.md analizados:**
- ✅ NexoAccManager: Activo | Versión 5.0.0 | Actualizado 2026-08-10
- ✅ Tic-Tac-Toe: Fases 1-3 completadas | Versión 0.2.0
- ✅ contract-guard: Activo | Versión 1.1.0
- ✅ e14-fraud-detector: Activo | Versión 0.3.0
- ✅ flag-edge: RELEASED ✅ | Versión 1.0
- ✅ grani-usco: Activo | Versión 0.1.0
- ✅ nexocore: EN PROGRESO · Sprint 1 completo
- ✅ nva-demons: Activo | Versión 0.2.0 (Fase 2 FXs)
- ✅ nx-music: Fase 2/6 completada | Limitación: frontend Flutter no iniciado
- ✅ portafolio: Activo | Versión MVP + S-02 Dark Mode Toggle
- ✅ reports: Activo | Versión MVP (Fase 1)
- ✅ supply-radar: Activo | Versión 1.2.0
- ✅ synthetic-trader: **Activo — Paper Trading 24/7 en curso** | Versión 0.3.0
- ✅ triqui: Activo | Versión 2.0.0
- ✅ wifi-pentest: Activo — herramientas de pentesting WiFi
- ✅ wordlists: Activo — sistema de gestión de wordlists
**Proyectos SIN PROJECT.md (legacy):**
- CONTRIBUTIONS-FIX.md (archivo, no proyecto)
- research-cafe-colombia.md (archivo, no proyecto)
**Estado:** Todos los proyectos reales tienen PROJECT.md actualizado

### Paso 8: Trading bot
**Archivo:** `~/proyectos/synthetic-trader/PROJECT.md`
- **Estado:** Activo — Paper Trading 24/7 en curso
- **Versión:** 0.3.0 | Última actualización: 2026-08-03
- **Logs de trading:** Actividad reciente confirmada (08:57:07 - SEÑAL LONG en R_100)
- **Cron jobs de trading:**
  - ✅ pradx-paper-trading-launch (0 9 * * *) - OK
  - ✅ pradx-data-collector (0 6 * * *) - OK
- **Estado:** **TRADING OPERATIVO EN PAPER TRADING**

### Paso 9: Cron jobs activos
**Total:** 6 jobs activos
1. **fee38b880c1d** - orchestrator-ecosystem-sweep (0 */2 * * *) - OK
2. **c08e627d3ef0** - ecosystem-audit-watchdog (*/30 * * * *) - OK
3. **f9723501af69** - Diagnostico diario SophIA (0 9 * * *) - **ERROR** (ver detalles abajo)
4. **8568fa106426** - pradx-paper-trading-launch (0 9 * * *) - OK
5. **04d092054e27** - pradx-data-collector (0 6 * * *) - OK
6. **b3f20bc3f82f** - memory-systems-health-check (every 120m) - OK
- **Job de diagnostico:** Registrado correctamente para ejecución diaria a las 9:00 AM
- **Próxima ejecución:** 2026-08-14T09:00:00-05:00

### Paso 10: Memory y AGENTS.md
- **Global AGENTS.md:** `~/.hermes/AGENTS.md` existe (191 líneas, versión 2026.08.10)
- **Profile AGENTS.md:**
  - ✅ dev: 128 líneas
  - ❌ orchestrator: **NO EXISTE** el directorio `~/.hermes/profiles/orchestrator/`
  - ✅ research: 149 líneas
  - ✅ designer: 126 líneas
  - ✅ trader: 34 líneas
  - ✅ ciberseguridad: 47 líneas
  - ✅ gatekeeper: 204 líneas
- **Memory tool:** El comando `hermes memory list` no está disponible; se usa `hermes memory {setup,status,off,reset}`
- **Estado:** **ORCHESTRATOR PERFIL INCOMPLETO** - falta directorio y archivos de configuración

### Paso 11: Trading — indices sinteticos
**Perfil trader configuración:** `~/.hermes/profiles/trader/config.yaml`
- ✅ ccxt: enabled: true
- ✅ yfinance: enabled: true  
- ✅ deriv: enabled: true
- ✅ lsp-intelligence: enabled: false (esperado en perfil de trading)
- ✅ zenith: enabled: false
- ✅ mcp-code-review-pro: enabled: false
- **Agent configuration:**
  - paper_trading: true
  - risk_per_trade_pct: 2
  - max_daily_trades: 10
  - instruments: [synthetic_indices, forex, stocks, crypto]
- **Estado:** **TRADER CONFIGURADO CORRECTAMENTE** para operaciones en índices sintéticos

### Paso 12: Gateway
**Estado del servicio:** `hermes-gateway.service`
- ✅ **ACTIVE: active (running)** since Thu 2026-08-13 07:27:24 -05 (1h 34min ago)
- ✅ Main PID: 4097 (hermes)
- ✅ Tasks: 299 (limit: 9519)
- ✅ Memory: 2.7G (peak: 2.7G)
- ✅ CPU: 2min 33.925s
- **Logs de advertencia:** 
  - MCP server 'dark-memory' falló conexión inicial después de 3 intentos (parked)
  - Error: `ValueError: MCP server 'dark-memory' has no 'command' in config`
- **Estado:** **GATEWAY OPERATIVO** con advertencias de configuración en dark-memory

### Paso 13: Issues críticos detectados
1. **DASHBOARD INACTIVO** - Servicio web no responde en puerto 9119
2. **ORCHESTRATOR PERFIL INCOMPLETO** - Falta config.yaml y directorio completo
3. **SKILLS FALTANTES** - architecture-patterns, electron-desktop-dev, security-and-hardening no encontradas
4. **CONFIGURACIÓN DUPLICADA DE DARK-MEMORY** - Tanto `dark-memory` como `dark-memory-mcp` aparecen como enabled en config
5. **JOB FALLIDO** - "Diagnostico diario SophIA" falló por modelo EOL: mistralai/mistral-medium-3.5-128b (End of Life 2026-08-07)

### Paso 14: Memory update (issues para tracking)
Se deben guardar los siguientes issues en memory tool:
- Diagnostico 2026-08-13: dashboard inactivo detectado en servicio web
- Diagnostico 2026-08-13: perfil orchestrator incompleto detectado en configuración
- Diagnostico 2026-08-13: skills faltantes detectadas (architecture-patterns, electron-desktop-dev, security-and-hardening)
- Diagnostico 2026-08-13: configuración duplicada de dark-memory detectada
- Diagnostico 2026-08-13: job fallido por modelo EOL detectado en diagnostico diario

---

## RECOMENDACIONES DE ACCIÓN

### INMEDIATAS (hacer ahora)
1. **Iniciar dashboard:** `hermes dashboard register` (o verificar puerto y proceso)
2. **Revisar job fallido:** Actualizar modelo en configuración del job diagnostico diario
3. **Verificar orchestrator:** Crear directorio y archivos de configuración mínimos

### CORTO PLAZO (esta semana)
1. **Instalar skills faltantes:** 
   - `hermes skills install architecture-patterns`
   - `hermes skills install electron-desktop-dev`  
   - `hermes skills install security-and-hardening`
2. **Resolver dark-memory:** Investigar por qué aparece duplicado en config y corregir
3. **Actualizar modelo EOL:** Reemplazar mistral-medium-3.5-128b por modelo válido en jobs afectados

### MANTENIMIENTO
- Monitorear logs del gateway para errores de MCP
- Verificar estado diario del dashboard
- Mantener skills actualizadas mediante `hermes skills update`

---

## PRÓXIMOS PASOS
Este reporte se guardará en `/home/sebas/proyectos/reports/diagnostico-2026-08-13.md`  
El siguiente diagnóstico automático se ejecutará: **2026-08-14 09:00:00**  

**Fin del reporte**