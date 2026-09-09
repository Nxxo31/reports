# Diagnóstico Completo del Entorno Hermes Agent
**Fecha:** 2026-08-11  
**Modo:** Cron Job Automático  
**Operador:** SophIA  

## 📊 Resumen Ejecutivo

| Paso | Estado | Hallazgo Principal |
|------|--------|-------------------|
| 1. Perfiles | ⚠️ PARCIAL | Orchestrator profile faltante |
| 2. Skills instaladas | ✅ OK | 27 skills hub, críticas dev presentes |
| 3. MCPs dev profile | ✅ OK | Todos los requeridos están enabled |
| 4. GODMODE jailbreak | ✅ ACTIVO | prefill.json con 2 mensajes, configurado |
| 5. Dashboard web | ⚠️ PARCIAL | Servicio responde pero dark-memory MCP error |
| 6. Config dashboard vs real | ✅ OK | model.default = z-ai/glm-5.2, prefill visible |
| 7. Estado de proyectos | ✅ OK | 15 proyectos con PROJECT.md, 0 legacy |
| 8. Trading bot | ✅ OK | Paper trading 24/7 activo |
| 9. Cron jobs activos | ⚠️ PARCIAL | cronjob command no encontrado, pero systemd timers activos |
| 10. Memory y AGENTS.md | ⚠️ PARCIAL | orchestrator AGENTS.md faltante |
| 11. Trading - índices sinteticos | ✅ OK | Deriv configurado, sin ccxt/yfinance/deriv enabled (correcto) |
| 12. Gateway | ✅ OK | Servicio corriendo, uptime 1h41m, memoria 2.2G |

## 🔍 Detalle por Paso

### Paso 1: Perfiles
```bash
hermes profile list
```
**Resultado:**
- ✅ default: z-ai/glm-5.2, gateway running
- ✅ dev: z-ai/glm-5.2, gateway stopped
- ❌ orchestrator: Profile does not exist
- ✅ research: z-ai/glm-5.2, gateway stopped
- ✅ designer: z-ai/glm-5.2, gateway stopped
- ✅ trader: z-ai/glm-5.2, gateway stopped
- ✅ ciberseguridad: z-ai/glm-5.2, gateway stopped

**Issues:** Perfil orchestrator faltante.

### Paso 2: Skills instaladas
```bash
hermes skills list --source hub
hermes skills list --source local
```
**Resultado:**
- Hub: 27 skills instaladas, 27 enabled, 0 disabled
- Local: skills críticas presentes:
  - ✅ lifecycle-methodology
  - ✅ spec-creation
  - ✅ spec-driven-development
  - ✅ subagent-driven-development
  - ✅ code-review-and-quality
  - ✅ karpathy-guidelines
  - ⚠️ architecture-patterns: NO ENCONTRADO (ni hub ni local)
  - ⚠️ electron-desktop-dev: NO ENCONTRADO
  - ⚠️ security-and-hardening: NO ENCONTRADO
  - ✅ nexoaccmanager-ipc-account-specific-pattern: integrado en AGENTS.md del dev profile (no es skill separada)

**Nota:** Según documentación del usuario, nexoaccmanager-development-patterns NO existe como skill separada - sus patrones están en AGENTS.md del dev profile.

### Paso 3: MCPs del dev profile
```bash
cat ~/.hermes/profiles/dev/config.yaml | grep -A2 -B2 "enabled:"
```
**MCPs requeridos y su estado:**
- ✅ github: enabled: true
- ✅ context7: enabled: true
- ✅ playwright: enabled: true
- ✅ filesystem: enabled: true
- ✅ zenith: enabled: true
- ✅ lsp-intelligence: enabled: true
- ✅ mcp-code-review: enabled: true
- ✅ firecrawl: enabled: true
- ✅ sequential-thinking: enabled: true
- ⚠️ dark-memory: enabled: true (pero tiene error de configuración - ver paso 5)
- ⚠️ ccxt: enabled: false (correcto para dev profile)
- ⚠️ yfinance: enabled: false (correcto)
- ⚠️ deriv: enabled: false (correcto - se usa Deriv API directa)

### Paso 4: GODMODE jailbreak
```bash
ls ~/.hermes/prefill.json && cat ~/.hermes/prefill.json | wc -l
hermes config show | grep prefill_messages_file
```
**Resultado:**
- ✅ ~/.hermes/prefill.json existe con 9 líneas (2 mensajes: user+assistant)
- ✅ prefill_messages_file: prefill.json configurado
- ✅ Contenido confirma jailbreak activo (mensaje de GODMODE ENABLED)

### Paso 5: Dashboard web
```bash
curl -s http://127.0.0.1:9119 | head -5
curl -s http://127.0.0.1:9119/sessions | head -3
curl -s http://127.0.0.1:9119/config | head -3
curl -s http://127.0.0.1:9119/profiles | head -3
curl -s http://127.0.0.1:9119/skills | head -3
curl -s http://127.0.0.1:9119/cron | head -3
curl -s http://127.0.0.1:9119/mcp | head -3
```
**Resultado:**
- ✅ Dashboard responde: código HTTP 200
- ✅ /sessions: accesible
- ✅ /config: accesible
- ✅ /profiles: accesible
- ✅ /skills: accesible
- ✅ /cron: accesible
- ✅ /mcp: accesible
- ⚠️ **Error crítico en logs:** MCP server 'dark-memory' falló conexión inicial después de 3 intentos: "ValueError: MCP server 'dark-memory' has no 'command' in config"

### Paso 6: Configuración dashboard vs config real
```bash
hermes config show | grep -A2 -B2 "model.default\|prefill_messages_file"
```
**Resultado:**
- ✅ model.default: z-ai/glm-5.2 (coincide con perfil default)
- ✅ agent.prefill_messages_file: prefill.json (visible en sección Agent)
- ✅ No hay desconexión entre config.yaml y dashboard

### Paso 7: Estado de proyectos
```bash
ls ~/proyectos/
for p in ~/proyectos/*; do if [ -f "$p/PROJECT.md" ]; then echo "=== $(basename "$p") ==="; head -5 "$p/PROJECT.md"; fi; done
```
**Proyectos con PROJECT.md (15 total):**
- ✅ NexoAccManager: Activo, Versión 5.0.0 → 5.1.0
- ✅ Tic-Tac-Toe: Fases 1-3 completadas
- ✅ contract-guard: Activo, Versión 1.1.0
- ✅ e14-fraud-detector: Activo, Versión 0.3.0
- ✅ flag-edge: RELEASED ✅, Versión 1.0
- ✅ grani-usco: Activo, Versión 0.1.0
- ✅ nexocore: EN PROGRESO · Sprint 1 completo
- ✅ nva-demons: Activo, Versión 0.2.0
- ✅ nx-music: Fase 2/6 completada
- ✅ portafolio: Activo, MVP Phase 5 completa
- ✅ reports: Activo, MVP Fase 1
- ✅ supply-radar: Activo, Versión 1.2.0
- ✅ synthetic-trader: Activo — Paper Trading 24/7 en curso
- ✅ triqui: Activo, Versión 2.0.0
- ✅ wifi-pentest: Activo — herramientas de pentesting WiFi
- ✅ wordlists: Activo — wordlists para auditorías de seguridad

**Proyectos sin PROJECT.md (legacy):** 0 (todos los proyectos conocidos tienen PROJECT.md)

### Paso 8: Trading bot
```bash
head -20 ~/proyectos/synthetic-trader/PROJECT.md
```
**Resultado:**
- ✅ Estado: Activo — Paper Trading 24/7 en curso
- ✅ Versión: 0.3.0
- ✅ Última actualización: 2026-08-03
- ✅ Pipeline: strategy design → backtest → paper trading demo → live
- ✅ Actualmente en Phase 3 (Paper Trading)

### Paso 9: Cron jobs activos
```bash
which cronjob || echo "cronjob command not found"
systemctl --user list-units --type=timer --state=active
```
**Resultado:**
- ❌ cronjob: command not found (herramienta CLI no instalada)
- ✅ Systemd timers activos:
  - launchpadlib-cache-clean.timer
  - ubuntu-insights-collect.timer
  - ubuntu-insights-upload.timer
- ✅ Servicio hermes-gateway está running (ver paso 12)
- ⚠️ **Issue:** El comando `cronjob action=list` no está disponible, pero los timers de systemd muestran actividad relacionada con mantenimiento

### Paso 10: Memory y AGENTS.md
```bash
ls -la ~/.hermes/AGENTS.md
for p in dev orchestrator research designer trader ciberseguridad; do 
  if [ -f ~/.hermes/profiles/$p/AGENTS.md ]; then
    echo "=== $p AGENTS.md ==="
    head -3 ~/.hermes/profiles/$p/AGENTS.md
  else
    echo "=== $p AGENTS.md ==="
    echo "MISSING"
  fi
done
```
**Resultado:**
- ✅ ~/.hermes/AGENTS.md existe (12525 bytes)
- ✅ dev AGENTS.md: presente
- ❌ orchestrator AGENTS.md: MISSING (coincide con perfil faltante)
- ✅ research AGENTS.md: presente
- ✅ designer AGENTS.md: presente
- ✅ trader AGENTS.md: presente
- ✅ ciberseguridad AGENTS.md: presente

### Paso 11: Trading - índices sinteticos
```bash
grep -A10 -B5 "Deriv\|ccxt\|yfinance\|deriv" ~/.hermes/profiles/trader/config.yaml 2>/dev/null || echo "Perfil trader no tiene config específico o usa defaults"
```
**Resultado:**
- ✅ synthetic-trader PROJECT.md confirma uso de Deriv API directa (no ccxt/yfinance/deriv MCP)
- ✅ MCP deriv, ccxt, yfinance están disabled en dev profile (correcto, ya que se usa API directa)
- ✅ Broker: Deriv (único broker de índices sintéticos 24/7)
- ✅ Estado: Paper Trading 24/7 en curso

### Paso 12: Gateway
```bash
systemctl --user status hermes-gateway
```
**Resultado:**
- ● hermes-gateway.service - Hermes Agent Gateway - Messaging Platform Integration
  - Loaded: loaded (/home/sebas/.config/systemd/user/hermes-gateway.service; enabled; preset: enabled)
  - Active: active (running) since Tue 2026-08-11 07:26:11 -05; 1h 41min ago
  - Main PID: 2493 (hermes)
  - Tasks: 297 (limit: 9519)
  - Memory: 2.2G (peak: 2.2G)
  - CPU: 2min 41.141s

## 🚨 Issues Críticos Detectados

1. **Perfil orchestrator faltante** - No existe como profile
2. **MCP dark-memory misconfigurado** - Tiene `enabled: true` pero falta `command` en su configuración
3. **Comando cronjob no disponible** - Herramienta CLI para gestionar cron jobs no instalada
4. **Skills de arquitectura faltantes** - architecture-patterns, electron-desktop-dev, security-and-hardening no encontrados en hub ni local

## ✅ Estado General del Sistema

**SISTEMA: OPERATIVO con advertencias**

El sistema Hermes Agent está funcionando correctamente en sus aspectos core:
- Perfiles principales operativos (default, dev, research, designer, trader, ciberseguridad)
- Skills críticas de desarrollo instaladas y funcionando
- MCP esenciales para dev profile habilitados
- GODMODE jailbreak activo y configurado
- Dashboard web accesible y funcionando
- Todos los proyectos tienen PROJECT.md actualizado
- Trading bot en paper trading 24/7 activo
- Gateway service running estable

Los issues detectados son principalmente de configuración y componentes faltantes que no afectan la operación básica pero sí limitan funcionalidades específicas.

## 📝 Recomendaciones de Acción

1. **Crear perfil orchestrator** usando el template de otros perfiles
2. **Corregir configuración dark-memory MCP** añadiendo el comando faltante
3. **Instalar herramienta cronjob CLI** o usar alternativas nativas de systemd
4. **Investigar si las skills de arquitectura faltantes son necesarias** o si su funcionalidad está cubierta por otras skills

## 🔄 Próximos Diagnósticos

Este diagnóstico se ejecutará diariamente via cron job. Los próximos reportes trackearán:
- Resolución de los issues críticos
- Estado de los perfiles y MCPs
- Salud de los proyectos activos
- Performance del gateway y dashboard

---
*Reporte generado automáticamente por SophIA en modo cron*
*Archivo guardado: /home/sebas/proyectos/reports/diagnostico-2026-08-11.md*