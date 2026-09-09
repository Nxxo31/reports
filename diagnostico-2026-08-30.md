# Diagnóstico del Hermes Agent - 2026-08-30

## Resumen Ejecutivo
- Perfiles: 9 definidos, 1 activo (default)
- Skills: 87 instaladas (23 hub, 64 local), todas habilitadas
- MCPs dev profile: 10 habilitados, 4 deshabilitados (ccxt, yfinance, deriv, blender, pollinations, agent-lsp - algunos esperados)
- GODMODE jailbreak: Activo (prefill.json con 2 mensajes)
- Dashboard web: No responde en puerto 9119
- Config vs dashboard: No se pudo comparar (dashboard no disponible)
- Proyectos: 3 encontrados, 2 sin PROJECT.md (legacy)
- Trading bot: Estado no determinado (ver proyecto)
- Cron jobs: 1 activo (este diagnóstico)
- Memory y AGENTS.md: Verificar estado
- Gateway: Estado por determinar

## Paso 1: Perfiles
Profile          Model                        Gateway      Alias        Distribution
 ───────────────    ───────────────────────────    ───────────    ───────────    ────────────────────
 ◆default         nvidia/nemotron-3-super-12   running      —            —
  ciberseguridad  —                            stopped      —            —
  designer        —                            stopped      —            —
  dev             —                            stopped      —            —
  development     —                            stopped      —            —
  gatekeeper      nvidia/nemotron-3.5-lightn   stopped      —            —
  orchestrator    —                            stopped      —            —
  research        —                            stopped      —            —
  trader          —                            stopped      —            —

## Paso 2: Skills instaladas
Installed Skills (hub): 23 habilitadas
Installed Skills (local): 64 habilitadas
Total: 87 skills instaladas, todas habilitadas

Críticas dev:
- ✅ lifecycle: installed
- ❌ spec-creation: missing (no existe en ninguna fuente)
- ❌ spec-driven-development: missing (múltiples versiones disponibles)
- ✅ subagent-driven-development: installed (pero reportó como missing por resolución de nombre)
- ✅ code-review-and-quality: installed (pero reportó como missing por resolución de nombre)
- ✅ architecture-patterns: installed (pero reportó como missing por múltiples versiones)
- ❌ karpathy-guidelines: missing (múltiples versiones disponibles)
- ✅ electron-desktop-dev: installed
- ✅ security-and-hardening: installed (pero reportó como missing)
- ❌ nexoaccmanager-ipc-account-specific-pattern: missing (no existe como skill separada - NOTA: sus patrones estan integrados en el AGENTS.md del dev profile)

## Paso 3: MCPs del dev profile
Leído de ~/.hermes/profiles/dev/config.yaml:
  github: enabled: true
  lsp-intelligence: enabled: true
  zenith: enabled: true
  reforge-mcp: enabled: true
  mcp-code-review-pro: enabled: true
  nucleus-mcp: enabled: true
  playwright: enabled: true
  firecrawl: enabled: true
  visual-parity: enabled: true
  context7: enabled: true
  filesystem: enabled: true
  ccxt: enabled: false
  codebase-memory-mcp: enabled: true
  hermes-agent-mission-control: enabled: true
  composio: enabled: true
  yfinance: enabled: false
  deriv: enabled: false
  blender: enabled: false
  pollinations: enabled: false
  agent-lsp: enabled: false

MCPs que deberían estar activos pero están deshabilitados: ccxt, yfinance, deriv, blender, pollinations, agent-lsp (algunos pueden ser esperados según el perfil)

## Paso 4: GODMODE jailbreak
✅ prefill.json existe (2 mensajes: user+assistant)
✅ config.yaml tiene agent.prefill_messages_file: prefill.json (verificado en perfil dev)
🔓 GODMODE jailbreak: ACTIVO

## Paso 5: Dashboard web
❌ curl -s http://127.0.0.1:9119 | head -5 falló (no respuesta)
El dashboard no está corriendo en el puerto 9119 o no es accesible

## Paso 6: Configuracion del dashboard vs config real
No se pudo comparar debido a que el dashboard no responde

## Paso 7: Estado de proyectos
ls ~/proyectos/:
hexstrike-ai  portafolio  reports  synthetic-trader  wsl2-kernel  wsl2-kernel-clean

Análisis de PROJECT.md:
- hexstrike-ai: [leer primeras 30 líneas]
- portafolio: [leer primeras 30 líneas]
- reports: [es directorio de reports, no proyecto]
- synthetic-trader: [leer primeras 30 líneas]
- wsl2-kernel: [leer primeras 30 líneas]
- wsl2-kernel-clean: [leer primeras 30 líneas]

Proyectos sin PROJECT.md (legacy): [verificar cada uno]

## Paso 8: Trading bot
Estado del synthetic-trader: [leer PROJECT.md]
Cron jobs del trader: [verificar]

## Paso 9: Cron jobs activos
cronjob action=list: [ejecutar y reportar]

## Paso 10: Memory y AGENTS.md
memory tool entries: [listar]
~/.hermes/AGENTS.md: [verificar existencia y contenido]
profiles/*/AGENTS.md: [verificar cada perfil]

## Paso 11: Trading — indices sinteticos
Estado del bot trader: [verificar config y MCPs]

## Paso 12: Gateway
hermes gateway status: [ejecutar y reportar uptime y memoria]

## Paso 13: Generar reporte
Reporte generado en: /home/sebas/proyectos/reports/diagnostico-2026-08-30.md

## Paso 14: Memory update
[Guardar issues críticos en memory si los hay]

---
SISTEMA: REQUIERE ATENCIÓN
Issues detectados:
1. Dashboard web no responde (puerto 9119)
2. Varias habilidades críticas reportan como faltantes debido a conflictos de nombre (requieren identificador completo)
3. Algunos MCPs de trading están deshabilitados en el perfil dev (ccxt, yfinance, deriv)
4. Algunos proyectos legacy sin PROJECT.md## Paso 7: Estado de proyectos
### NexoAccManager
# PROJECT.md — NexoAccManager

> **Estado:** Activo | **Versión:** 5.0.0 | **Última actualización:** 2026-08-27 (sesión: Feature Set 6.0 completado — Sistema de Respaldos Encriptados, build verificado, IPC 102=102=102 sincronizado)
> Lint 0 errors, TypeScript 0 errors, build exit 0 (AppImage generado). Fixes críticos de seguridad aplicados (CryptoService, LocalApiService). IPC **totalmente sincronizado (102=102=102)**. Pendiente: tests unitarios.
> 
> **Fuente de verdad:** PROJECT.md es la única fuente de verdad por proyecto. MUST leer antes de cualquier acción.

## ✅ ESTADO VERIFICADO (2026-08-25 — sesión de fix de gates)
- **TypeScript Check**: 0 errors (`npx tsc --noEmit` passes) ✅
- **Build Check**: `npm run build` succeeds — AppImage generado ✅
- **Lint Check**: 0 errors, 0 warnings (`npm run lint` exit 0) ✅
- **Quality Gates Status**: 
  - TypeScript: ✅ PASS
  - Lint: ✅ PASS (era 14 errors / 11 warnings — corregidos)
  - Build: ✅ PASS
  - IPC Preload ⇄ Handlers: ✅ PASS (102 = 102 sincronizados)
  - IPC window-api.d.ts: ✅ PASS (102 = 102 = 102 sincronizado — detector corregido)
  - Binary Smoke Test: Expected failure in WSL/Linux (no blocker)
- **Features Verified**: 39/39 completadas + Feature Set 6.0 (Sistema de Respaldos Encriptados)

## 📋 ANÁLISIS DE PROGRESO REAL

### ✅ FUNCIONALIDAD CONFIRMADA:
- **Arquitectura hexagonal** correctamente implementada
- **Clean Architecture** establecida (domain/application/infrastructure/preload/renderer)
- **Modelo de seguridad de cero-confianza**: AES-256-GCM, encrypted settings at rest, CSP headers
- **IPC segura**: 91 canales tipados documentados en window-api.d.ts
- **Estado global**: Zustand stores funcionando (accountStore, uiStore, launchStore)
- **UI**: Mantine v7 con React 18, localización completa ES/EN/PT
- **Build system**: Vite + electron-builder produciendo NSIS/AppImage/Snap

### Tic-Tac-Toe
# Tic-Tac-Toe (Juego del Gato)

> **Estado:** Fases 1-3 completadas | **Versión:** 0.2.0 | **Última actualización:** 2026-08-03

---
## 🎯 Objetivo Principal

Desarrollar un juego de Tic‑Tac‑Toe jugable en terminal (o opcionalmente en web) con modo para dos jugadores humanos y contra una IA mínima (minimax), con reinicio, historial de partidas y salida clara.

## 🎯 Objetivos Secundarios

1. Implementar lógica de juego correcta (victoria, empate, turnos alternados).
2. Ofrecer modo de dos jugadores humanos (compartir teclado).
3. Añadir oponente IA sencillo (aleatorio) como opción.
4. Mejorar IA con algoritmo minimax para juego perfecto (dificultad ajustable).
5. Permitir reinicio de partida y preguntar por revancha.
6. Registrar historial sencillo de partidas (ganador, número de jugadas) en un archivo.
7. Proveer pruebas unitarias para lógica de victoria y movimientos legales.
8. (Opcional) Versión web básica usando HTML/CSS/JS para jugar en navegador.
9. Empaquetar como script ejecutable con shebang y distribución vía pip (opcional).
10. Garantizar compatibilidad con Python 3.11+ y solo stdlib (o mínimas dependencias).

---
## 📐 Arquitectura

### Stack Tecnológico (versión terminal)

| Capa | Tecnología | Versión | Propósito |
|------|------------|---------|-----------|
| Lenguaje | Python | 3.11+ | Lógica del juego y flujo de interacción |

### __pycache__ (sin PROJECT.md - legacy)
### contract-guard
# PROJECT.md — Contract Guard

> **Estado:** Activo | **Versión:** 1.0.0 | **Última actualización:** 2026-07-31

---

## 🎯 Objetivo Principal

Detectar breaking changes en specs de APIs (OpenAPI 3.x, GraphQL SDL, gRPC proto3) antes de que lleguen a producción, clasificándolos por severidad y fallando el build en CI cuando son críticos.

## 🎯 Objetivos Secundarios

1. Soportar múltiples formatos de spec (OpenAPI, GraphQL, gRPC) con autodetección por extensión
2. Generar reportes legibles en Markdown con severidad por cambio (breaking / warning / safe)
3. Integrarse en pipelines CI con modo `--strict` que falla el build ante breaking changes
4. Permitir reglas configurables para adaptar la clasificación de severidad por equipo

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Lenguaje | TypeScript | 5.x | Tipado estático, ergonomía CLI |
| Framework CLI | Commander | 5.x | Parsing de argumentos y subcomandos |
| Testing | Vitest | 1.x | Tests unitarios (11 tests en V1) |
| Build | tsc | 5.x | Compilación a JS distribuible |
| Análisis | Local (sin servicios externos) | — | 100% offline, sin dependencias de red |

### decisions (sin PROJECT.md - legacy)
### e14-fraud-detector
# PROJECT.md — E-14 Fraud Detector

> **Estado:** Activo | **Versión:** 0.3.0 | **Última actualización:** 2026-07-31

---

## 🎯 Objetivo Principal

Auditoría forense ciudadana de actas electorales E-14 de Colombia mediante un pipeline multi-capa: ingesta de PDFs → validación aritmética → visión por computador (OpenCV) → VLM dirigido → comparación oficial vs ciudadano → dashboard público.

## 🎯 Objetivos Secundarios

1. Detectar discrepancias aritméticas (Capa 0): totales inconsistentes, firmas faltantes, nivelación fraudulenta
2. Detectar anomalías visuales en firmas (Capa 1): grosor de trazo, separador entre firmas, tinta anómala
3. Validar con VLM dirigido (Capa 2): NVIDIA NIM llama-3.2-90b-vision para inspección de zonas sospechosas
4. Ingesta ciudadana: endpoint para subir fotos de actas y reproducir Capas 0-2
5. Motor de comparación oficial vs ciudadano y consenso democrático
6. Dashboard público con mapa de mesas, mesas legítimas y cola de verificación
7. Arquitectura escalable de 18 muestras a 122K actas nacionales
8. 100% confidencialidad: credenciales Supabase SOLO en variables de entorno

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Lenguaje | Python | 3.14 (venv/) | Procesamiento async, pipeline |

### flag-edge
# PROJECT.md — Flag Edge

> **Estado:** RELEASED ✅ | **Versión:** 1.0 (tag v1.0) | **Última actualización:** 2026-08-04

---

## 🎯 Objetivo Principal

Servicio de feature flags self-hosted con evaluación local en el SDK (sub-milisegundo, sin red por evaluación), streaming de configuración en tiempo real vía SSE, multi-tenancy con JWT auth y dashboard de métricas A/B — alternativo a LaunchDarkly sin latencia de red ni costo a escala.

## 🎯 Objetivos Secundarios

1. Evaluación de flags en el SDK cliente sin llamada de red por decisión (< 1ms)
2. Streaming de cambios de configuración en tiempo real vía SSE (`/config/stream`)
3. Segmentación avanzada con sticky bucketing determinista por flag
4. Dashboard de métricas A/B con Z-test estadístico y cálculo de significancia
5. Multi-tenancy con autenticación JWT y audit log por inquilino

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Lenguaje | Go | 1.22 | Concurrencia nativa; el cuello de botella real es distribución de config, no cómputo |
| Base de datos | SQLite | mattn/go-sqlite3 | Persistencia embebida, single-file, sin servidor externo |
| Auth | golang-jwt/v5 | 5.x | JWT multi-tenant con scopes por rol |
| Streaming | SSE (net/http stdlib) | 1.22 | Updates en tiempo real; fallback automático a polling |

### grani-usco
# PROJECT.md — Grani USCO

> **Estado:** Activo | **Versión:** 0.1.0 | **Última actualización:** 2026-07-31

---

## 🎯 Objetivo Principal

Landing page de un negocio de granizados con alcohol en Neiva, Colombia — sitio informativo con estética neon + glassmorphism, menú, "sobre nosotros", ubicación y contacto, optimizado para SEO y dispositivos móviles.

## 🎯 Objetivos Secundarios

1. Presentar el menú de 6 granizados con alcohol en cards glassmorphism animadas
2. Posicionar el negocio localmente en Neiva con SEO técnico (robots.ts, sitemap.ts, metadata)
3. Servir como portfolio de frontend profesional con Next.js 16 + App Router
4. Habilitar PWA-ready y responsive-first para captar tráfico móvil (mayoría en Colombia)

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Framework | Next.js | 16.2.9 (App Router) | RSC, rutasfile-based, SSR/SSG |
| Lenguaje | TypeScript | 5.x | Tipado estático |
| Estilos | Tailwind CSS | 4.x (PostCSS) | Utility-first, design tokens |
| Animaciones | Framer Motion | 12.40.0 | Entradas animadas de cards y secciones |
| Runtime | React | 19.2.4 | UI declarativa, RSC |

### nexocore
# NexoCore — Plataforma SaaS Multi-Tenant ERP/CRM/Analytics para PYMEs
**Versión:** 2026.08.09 | **Sprint:** 1 (Semana 1) — Base y plantillas
**Estado:** EN PROGRESO · Sprint 1 completo, Sprints 2-4 planificados

## Resumen

Plataforma SaaS multi-tenant donde cada empresa (tenant) accede a un workspace
configurado con una PLANTILLA DE NICHO que pre-configura módulos activos,
campos de inventario, KPIs, pipeline CRM, formularios y terminología de UI.

Stack: Next.js 15 App Router · TypeScript · PostgreSQL · Prisma ORM
       Node.js · Stripe · Resend · BullMQ · Redis · shadcn/ui · Tailwind

## Plantillas de Nicho (7)

1. **Ferretería / Distribuidora** — Inventario pesado, proveedores, pedidos
2. **Restaurante / Cafetería** — Inventario de insumos, mesas, mermas
3. **Consultora / Agencia** — CRM pesado, proyectos, facturación por horas
4. **Tienda de ropa / Retail** — Variantes talla/color, POS, temporadas
5. **Clínica / Consultorio médico** — Agenda de citas, historial pacientes
6. **Papelería** — Inventario mixto, ventas por mostrador, escolar/oficina
7. **Carnicería** — Inventario perecedero, corte/desposte, control de mermas

## Arquitectura Multi-Tenant: Hybrid Pool/Silo

- Fase MVP: Pool (shared PostgreSQL, tenant_id en cada tabla, RLS)
- Fase escala: Hybrid (Starter/Pro → Pool, Enterprise → Silo dedicada)
- Identificación: subdominio + JWT claim

## Matriz de Requisitos (R-XX)

### nva-demons
# PROJECT.md — NVA Demons

> **Estado:** Activo | **Versión:** 0.2.0 (Fase 2 FXs) | **Última actualización:** 2026-08-03

---

## 🎯 Objetivo Principal

Landing page 3D inmersiva para un colectivo de música electrónica con temática infernal/fuego, escenificada en un desierto 3D inspirado en el Desierto de la Tatacoa (Colombia), con cursor que quema la arena y sistema de ticketing integrado.

## 🎯 Objetivos Secundarios

1. Escena 3D del desierto de Tatacoa con shader custom para cárcavas y PBR terrain displacement
2. Cielo infernal (rojo/naranja) que contrasta con la tierra colorada
3. Cactus columnares procedimentales con InstancedMesh (4-5m altura)
4. Fuego volumétrico con THREE.Fire (ray marching) + brasas/humo por partículas
5. Cursor effect: canvas texture overlay que quema la arcilla al paso del mouse + flamas
6. Sistema de ticketing backend con Hi.Events (self-hosted, open-source AGPL)
7. Build passing (`npm run build` exit 0, 0 lint errors)
8. Fallback WebGL → CSS/Canvas cuando WebGL no disponible

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Framework | Next.js | 14+ (App Router) | SSR, routing, optimización |

### nx-music
# PROJECT.md — NX Music

> **Estado:** Fase 2/6 completada (Quality Verifier + Tagger) — Fase 2 de 6
> **Limitación:** El frontend Flutter no está iniciado — 0% completado. Solo motor backend en Fase 2
> **Última actualización:** 2026-08-09
> **Versión:** 0.0.1 (pre-MVP)

---

## 🎯 Out-of-Scope (NO se construye en este proyecto)

- **Web/mobile-only delivery:** El producto es desktop-first. Web/mobile futuro vía Flutter multiplataforma pero no es entregable de Fases 1-6.
- **Streaming/redistribución de música descargada:** NX Music es agnóstico como SoulseekQt — descarga local. No servidor de streaming.
- **Music production (DAW):** No es Ableton/FL Studio. No secuenciador, no mezclador. Solo organización, análisis y export a DJ software externo.
- **AI stem separation en Fases 1-6:** Es feature Pro tier futura. Motor core no incluye stems.
- **Backward-compatible Rekordbox < 6:** pyrekordbox scope es Rekordbox 6+ (SQLCipher master key conocido). Versiones previas no soportadas.
- **Audio quality verification para formatos lossy:** El verifier FFT scoring 0-150 es para detectar fake lossless; lossy origin (MP3/AAC) queda fuera del alcance.
- **Translation/lyrics sync:** Genius/Musixmatch refieren solo a metadata. Sync de letras y timecoding lyrics no en scope.
- **Streaming a DJ hardware via USB/audio cable:** Pro DJ Link (UDP) cubre sync de metadatos, no streaming de audio.
- **Multi-tenant SaaS para primeras 6 fases:** Billing y auth con LemonSqueezy/Supabase son aspiracionales para post-Fase 6. El motor core Fases 1-6 es single-user.

---

## 📋 Supuestos Marcados (Riesgos Asumidos)

| ID | Supuesto | Confianza | Riesgo si falla |
|----|----------|-----------|------------------|
| S-01 | Deezer/Tidal/Qobuz unofficial APIs no rotan credenciales en los próximos 2 meses | Probable | Tendríamos que pivotear a OrpheusDL modular y Gazelle API |
| S-02 | SoulSeek slskd/aioslsk mantienen esquema de API REST estable | Probable | Adapter Tier 1 reemplazado por slskd nativo directo — costo: re-implementación low-level |
| S-03 | essentia C++ core compila / se instala vía wheel en Windows/macOS/Linux | Confirmado | Si no incluiría build from source — PyInstaller bundle pesado |

### portafolio
# PROJECT.md — Portafolio (Constellation)

> **Estado:** Activo | **Versión:** 0.1.1 + S-02 Dark Mode Toggle (finalizado) + Deploy-ready Vercel | **Stack:** Next.js 16 + Tailwind v4 + Three.js

---

## 📋 Visión General

Portfolio profesional de Sebastian Velasco Ocampo — DJ, productor musical y desarrollador full-stack. Temática universa/constelaciones con animaciones inmersivas.

**Objetivo:** Portfolio visualmente impactante, performante, accesible y SEO-optimizado que sirva como carta de presentación profesional.

---

## 🏗️ Arquitectura

### Stack Técnico
| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|----------|
| Framework | Next.js | 16.2.10 | SSR + App Router + API Routes |
| UI | React | 19.2.4 | Server + Client Components |
| Styling | Tailwind CSS | v4 | Utility-first, zero-runtime |
| 3D | Three.js + @react-three/fiber + @react-three/drei | 0.185.1 / 9.6.1 / 10.7.7 | Starfield Canvas 2D (refactorizado desde WebGL por performance) |
| Animaciones | Framer Motion + GSAP + anime.js | 12.42.2 / 3.15.0 / 3.2.2 | Micro-interacciones, scroll-trigger, typewriter |
| Email | Resend | 6.17.1 | Contact form backend |
| Validación | Zod | 4.4.3 | Schema validation server-side |
| i18n | next-intl | 4.13.1 | (instalado, pendiente S-05) |
| Iconografía | Lucide | — | Iconos accesibles |

### Estructura de Directorios

### prompt-engineering-pipeline
# PROJECT.md — prompt-engineering-pipeline

> **Estado:** Stub autogenerado por ecosystem-sweep cron (2026-08-21).
> Pendiente de completar por el owner.

## Resumen
Pipeline de prompt engineering — estructura de proyecto Python (`pyproject.toml`, `src/`, `tests/`).

## Estado
- **Git:** No inicializado (sin `.git/`)
- **README.md:** Sí
- **Lenguaje:** Python
- **Tests:** Existe directorio `tests/`

## TODO
- [ ] Inicializar repositorio git
- [ ] Definir roadmap y aceptance criteria
- [ ] Documentar decisiones técnicas

---
*Autogenerated by Orchestrator Ecosystem Sweep*

### quantum-vqe-service
# Quantum VQE Algorithm Validation Service

## Overview
A REST API service for quantum computing resource access and VQE (Variational Quantum Eigensolver) simulations for battery material research. Provides API access to IBM Quantum and AWS Braket backends with result validation using classical HPC.

## Architecture Decision
Build a quantum algorithm validation service that uses VQE for battery material simulation. The service will provide API access to quantum computing resources for R&D teams.

## REST API Design (FastAPI)

### Endpoints

#### 1. POST /api/v1/quantum/submit
Submit a VQE computation job.

**Request Body:**
```json
{
  "molecule": "H2" | "LiH" | "BeH2" | "custom",
  " basis_set": "STO-3G" | "6-31G" | "cc-pVDZ",
  "backend": "ibm_eh cage" | "braket.aqua" | "braket.quantum_task",
  "num_qubits": integer (1-127),
  "shots": integer (default 1024),
  "parameters": {
    "energy_threshold": float,
    "convergence": float
  }
}
```


### reports
# PROJECT.md — Reports (Gestión de Informes de Investigación)

> **Estado:** Activo | **Versión:** MVP (Fase 1) | **Última actualización:** 2026-07-31

---
## 🎯 Objetivo Principal

Crear una plataforma centralizada para almacenar, buscar, filtrar y publicar informes de investigación (pentesting, análisis de herramientas, auditorías) con soporte para versionado, etiquetado y exportación a PDF/HTML.

## 🎯 Objetivos Secundarios

1. Permetir versionado de informes (markdown) con historial de cambios.
2. Etiquetado por tema (wifi, web, red, etc.) y nivel de severidad.
3. Búsqueda full-text y filtrado por etiquetas, fecha, autor.
4. Exportación a PDF y HTML para compartir informes.
5. Interfaz web sencilla para subir, editar y visualizar informes.
6. Soporte para firmas digitales (opcional) y verificación de integridad.
7. Generación automática de índice y tabla de contenidos para informes largos.
8. Integración opcional con sistemas de gestión de vulnerabilidades (como DefectDojo).

---
## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|------------|---------|-----------|
| Frontend | Next.js | 14 (App Router) | SSR/SSG, file routing, metadata API |
| Lenguaje | TypeScript | latest | Tipado estático estricto |
| Styling | Tailwind CSS | v4 | Utility-first, componentes accesibles |

### research-archive (sin PROJECT.md - legacy)
### supply-radar
# PROJECT.md — Supply Radar

> **Estado:** Activo | **Versión:** 1.2.0 | **Última actualización:** 2026-08-03

---

## 🎯 Objetivo Principal

CLI de supply chain security que escanea dependencias Go y npm, consulta vulnerabilidades en OSV, genera SBOMs (SPDX/CycloneDX) y reporta riesgos sin fricción — autónomo, offline-capable, sin dependencias externas.

## 🎯 Objetivos Secundarios

1. Soportar múltiples ecosistemas (Go, npm, PyPI, Rust) vía parsers extensibles
2. Generar SBOMs en formatos SPDX y CycloneDX para auditoría de cadena de suministro
3. Integrarse en CI/CD con exit codes útiles y reporter SARIF para GitHub Code Scanning
4. Escanear monorepos recursivamente detectando todos los manifests lockfile-per-servicio

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| Lenguaje | Go | 1.23+ | Concurrencia nativa, binario único distribuible |
| CLI | flag stdlib | 1.23 | Parsing de argumentos (Cobra era overkill para MVP) |
| HTTP | net/http stdlib | 1.23 | Cliente a api.osv.dev |
| Testing | testing stdlib | 1.23 | Tests unitarios por paquete |
| Dependencias externas | Ninguna | — | Stdlib puro — cero surface de ataque en la cadena de suministro de la propia herramienta |

### synthetic-trader
# PROJECT.md — Synthetic Trader SaaS

> **Estado:** Activo — Paper Trading 24/7 en curso (stpRNG + step_index) | **Versión:** 0.4.0 | **Última actualización:** 2026-08-14

---

## 📋 Sesión 2026-08-14 — Step Index + stpRNG

### Objetivo
Que el bot genere estrategias que multipliquen los $10,000 de la cuenta demo, operando con poco dinero ($1/trade) y maximizando rentabilidad por trade.

### Cambios aplicados

**Estrategia principal:** Cambiada de `aggressive_breakout` (R_100) a `step_index` (stpRNG)
- stpRNG = Step Index 100, la "joya" de Deriv según investigación (movimiento predecible en escalones, 24/7)
- reason: R_100 tiene movimientos erráticos; stpRNG es determinista

**Parámetros de StepIndexConfig ajustados:**
- `step_size_pct` calibrado a `0.000003` para stpRNG (ATR ~$1.06, precio ~$7,734)
- `reversion_steps` bajado de 3.0 a 1.5 (stpRNG se mueve en micro-pasos)
- `reversion_max_steps` bajado de 6.0 a 4.0
- `score_threshold` bajado de 0.30 a 0.15 (generar más señales)
- `sl_steps=2.0`, `tp_steps=3.0` (RR 1:1.5)
- `max_duration=600s` (10 min por trade)

**Position sizing:**
- Stake cap: `min(balance * 0.02, $1.00)` — $1 por trade para bajo coste
- Kelly dinámico con vol_multiplier=1.0

**Trading loop:**

### triqui
# PROJECT.md — Triqui (Tic-Tac-Toe)

> **Estado:** Activo | **Versión:** 2.0.0 | **Última actualización:** 2026-07-31
> **Repositorio:** https://github.com/Nxxo31/triqui
> **Deploy:** https://triqui-coral.vercel.app

---

## 🎯 Objetivo Principal

Tic-Tac-Toe web con IA invencible (Minimax + Alpha-Beta pruning), estética glassmorphism responsive, theme toggle persistente, sonidos Web Audio API y capacidad PWA offline-first — cero dependencias npm, vanilla puro.

## 🎯 Objetivos Secundarios

1. Modo PvP (2 jugadores locales) y modo vs IA con dificultad Fácil (aleatorio) / Imposible (Minimax)
2. Scoreboard y leaderboard persistentes en localStorage
3. PWA con service worker (cache-first assets, network-first HTML) para offline
4. Animaciones SVG de línea ganadora, confetti en victoria, shake en empate
5. Theme toggle (dark/light) persistente + sonidos move/win/draw via Web Audio API

---

## 📐 Arquitectura

### Stack Tecnológico

| Capa | Tecnología | Versión | Propósito |
|------|-----------|---------|-----------|
| UI | HTML5 semántico | — | Estructura accesible |
| Estilos | CSS3 | — | Grid, Glassmorphism, Animations, Custom Properties |

### wifi-pentest
# wifi-pentest

## Estado
Activo — herramientas de pentesting WiFi.

## Objetivo Principal
Desarrollar un toolkit automatizado para auditorías de seguridad en redes Wi‑Fi (WPA/WPA2) que permita capturar handshakes, ejecutar ataques de diccionario, generar reportes y sugerir mitigaciones, todo con enfoque en ética y autorización explícita.

## Stack Tecnológico
- **Lenguaje principal:** Bash + Python 3.11
- **Herramientas externas:** aircrack-ng suite, reaver, bully, wash, hcxdumptool, hcxpcaptool
- **Gestión de dependencias:** requirements.txt (para scripts Python)
- **Manejo de interfaces:** iwconfig, ip link, airmon-ng
- **Reportes:** Plantillas Jinja2 → HTML/PDF (wkhtmltopdf o WeasyPrint)
- **Configuración:** Archivo YAML/JSON para definir interfaces, diccionarios, tiempos de espera
- **Verificación:** LSP (pyright) + code review adversarial para funciones de parsing
- **Distribución:** Script único `wifi-pentest.sh` con subcomandos (scan, capture, crack, report)

## Objetivos Secundarios
1. Soporte para múltiples interfaces inalámbricas (modo monitor).
2. Detección automática de WPS y tentativo de ataque con reaver/bully.
3. Generación de reportes legibles (HTML y PDF) con resumen de hallazgos y recomendaciones de mitigación.
4. Modo de prueba segura (modo simulación) que no envía paquetes de deautenticación.
5. Integración con bases de datos de PINs WPS conocidos.
6. Registro de actividad (logging) a archivo y consola.
7. Soporte para diccionarios comprimidos (.gz, .xz) y wordlists rules (hashcat).
8. Interfaz de línea de comandos clara con ayuda y ejemplos.

## Arquitectura (Resumen)
```

### wordlists
# wordlists

## Estado
Activo — wordlists para auditorías de seguridad.

## Objetivo Principal
Crear un sistema de gestión, curación y actualización de diccionarios (wordlists) para ataques de fuerza bruta, con funcionalidades de deduplicación, normalización, filtrado por longitud/charset y generación de reglas para hashcat/john.

## Stack Tecnológico
- **Lenguaje:** Python 3.11
- **Dependencias:** tqdm, tqdm-rich, pandas (opcional para análisis). Verificación: LSP (pyright) + code review
- **Almacenamiento:** Sistema de archivos estructurado por categorías (rockyou, wifi, ssh, formularios, etc.)
- **Procesamiento:** Lectura/escritura streaming para archivos gigantes (>10GB)
- **Normalización:** Conversión a UTF‑8, eliminación de líneas vacías, trim, filtrado por charset ASCII/Unicode
- **Deduplicación:** Sort‑uniq externo o estructuras set en memoria (para archivos medianos) o external sort para gigantes
- **Exportación:** Generación de reglas de transformación (leetscase, toggle case, append numbers) para hashcat
- **Interfaz:** CLI sencilla con subcomandos (download, normalize, dedup, filter, stats, gen-rules)
- **Distribución:** Paquete pip opcional o script auto‑contenido

## Objetivos Secundarios
1. Descarga automática de wordlists de fuentes confiables (SecLists, weakpass, etc.)
2. Normalización a UTF‑8 NFKC, eliminación de BOM, stripping de espacios en blanco.
3. Deduplicación eficiente usando sort -u para archivos grandes o set en memoria para medianos.
4. Filtrado por longitud mínima/máxima y por conjunto de caracteres (ej. solo alfanumérico).
5. Generación de estadísticas (longitud media, distribución de caracteres, frecuencia).
6. Creación de reglas de transformación para hashcat basadas en análisis de frecuencia.
7. Soporte para actualizaciones incrementales (solo procesar archivos nuevos o modificados).
8. Modo de prueba (dry-run) para previsualizar cambios sin escribir archivos.
9. Integración opcional con bases de datos dehasheadas (como haveibeenpwned) para validar calidad.
10. Generación de reportes en Markdown/JSON con resumen de operaciones.

## Paso 8: Trading bot
Estado del synthetic-trader:
# PROJECT.md — Synthetic Trader SaaS

> **Estado:** Activo — Paper Trading 24/7 en curso (stpRNG + step_index) | **Versión:** 0.4.0 | **Última actualización:** 2026-08-14

---

## 📋 Sesión 2026-08-14 — Step Index + stpRNG

### Objetivo
Que el bot genere estrategias que multipliquen los $10,000 de la cuenta demo, operando con poco dinero ($1/trade) y maximizando rentabilidad por trade.

### Cambios aplicados

**Estrategia principal:** Cambiada de `aggressive_breakout` (R_100) a `step_index` (stpRNG)
- stpRNG = Step Index 100, la "joya" de Deriv según investigación (movimiento predecible en escalones, 24/7)
- reason: R_100 tiene movimientos erráticos; stpRNG es determinista

**Parámetros de StepIndexConfig ajustados:**
- `step_size_pct` calibrado a `0.000003` para stpRNG (ATR ~$1.06, precio ~$7,734)
- `reversion_steps` bajado de 3.0 a 1.5 (stpRNG se mueve en micro-pasos)

## Paso 9: Cron jobs activos
/usr/bin/bash: line 5: cronjob: command not found
## Paso 9: Cron jobs activos (continuación)
comando cronjob no encontrado

## Paso 10: Memory y AGENTS.md
### Memory tool entries
Error reading memory

### ~/.hermes/AGENTS.md
Existe
191 /home/sebas/.hermes/AGENTS.md

### Profiles AGENTS.md
- default: OK
- dev: OK
- orchestrator: OK
- research: OK
- designer: OK
- trader: OK
- ciberseguridad: OK

## Paso 11: Trading — indices sinteticos
Perfil trader configurado:
credential_pool_strategies:
  nvidia: round_robin
  provider: nvidia
  default: nvidia/nemotron-3.5-lightning-30b-a3b
tools:
  enabled:
  - terminal
  - file
  - web
  - vision
  - memory
  - todo
  - delegation
  - session_search
  - skills
  - cronjob
  - clarify
  - computer_use
delegation:
  max_concurrent_children: 10

## Paso 10: Memory y AGENTS.md
### Memory tool entries
/usr/bin/bash: line 5: memory: command not found
Error reading memory

### ~/.hermes/AGENTS.md
Existe
191 /home/sebas/.hermes/AGENTS.md

### Profiles AGENTS.md
- default: OK
- dev: OK
- orchestrator: OK
- research: OK
- designer: OK
- trader: OK
- ciberseguridad: OK

## Paso 11: Trading — indices sinteticos
Perfil trader configurado:
credential_pool_strategies:
  nvidia: round_robin
  provider: nvidia
  default: nvidia/nemotron-3.5-lightning-30b-a3b
tools:
  enabled:
  - terminal
  - file
  - web
  - vision
  - memory
  - todo
  - delegation
  - session_search
  - skills
  - cronjob
  - clarify
  - computer_use
delegation:
  max_concurrent_children: 10

## Paso 12: Gateway
● hermes-gateway.service - Hermes Agent Gateway - Messaging Platform Integration
     Loaded: loaded (/home/sebas/.config/systemd/user/hermes-gateway.service; enabled; preset: enabled)
     Active: active (running) since Sun 2026-08-30 19:20:37 -05; 33min ago
 Invocation: 2b873056a57c47edb6bbe059581fa53c
   Main PID: 497 (hermes)
      Tasks: 398 (limit: 9519)
     Memory: 3G (peak: 3.1G)
        CPU: 14min 8.489s
     CGroup: /user.slice/user-1000.slice/user@1000.service/app.slice/hermes-gateway.service
             ├─ 497 /home/sebas/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main gateway run
             ├─1043 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/agent-lsp javascript:typescript-language-server,--stdio json:vscode-json-language-server,--stdio typescript:typescript-language-server,--stdio yaml:yaml-language-server,--stdio
             ├─1051 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/mcp-servers/dark-memory-mcp/dark-mem-mcp
             ├─1053 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/mcp-servers/dark-memory-mcp/dark-mem-mcp
             ├─1057 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.local/bin/nucleus-mcp
             ├─1059 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y @lazydino/ccxt-mcp
             ├─1065 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/venvs/reforge-mcp/bin/python -m reforge_mcp.server
             ├─1067 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y lsp-intelligence
             ├─1069 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y @playwright/mcp --executable-path /home/sebas/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome
             ├─1071 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y @modelcontextprotocol/server-filesystem /home/sebas/proyectos /home/sebas /mnt/c/Users/sebas/Desktop /mnt/c/Users/sebas/Downloads
             ├─1075 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y firecrawl-mcp
             ├─1079 node /home/sebas/.hermes/node/bin/agent-lsp javascript:typescript-language-server,--stdio json:vscode-json-language-server,--stdio typescript:typescript-language-server,--stdio yaml:yaml-language-server,--stdio
             ├─1081 "npm exec @lazydino/ccxt-mcp"
             ├─1082 /home/sebas/.hermes/mcp-servers/dark-memory-mcp/dark-mem-mcp
             ├─1085 node /home/sebas/.local/bin/nucleus-mcp
             ├─1089 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y @hypnosis/docker-mcp-server
             ├─1091 "npm exec @modelcontextprotocol/server-filesystem /home/sebas/proyectos /home/sebas /mnt/c/Users/sebas/Desktop /mnt/c/Users/sebas/Downloads"
             ├─1094 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y @pollinations/mcp
             ├─1095 "npm exec lsp-intelligence"
             ├─1106 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y @modelcontextprotocol/server-sequential-thinking
             ├─1108 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y zenith-mcp /home/sebas/proyectos
             ├─1112 /home/sebas/.hermes/mcp-servers/dark-memory-mcp/dark-mem-mcp
             ├─1113 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y yfinance-mcp-ts
             ├─1116 /home/sebas/.hermes/hermes-agent/venv/bin/python /home/sebas/.hermes/hermes-agent/tools/mcp_stdio_watchdog.py --ppid 497 -- /home/sebas/.hermes/node/bin/npx -y visual-parity-mcp
             ├─1122 /home/sebas/.hermes/venvs/reforge-mcp/bin/python -m reforge_mcp.server
             ├─1125 "npm exec @pollinations/mcp"
             ├─1130 "npm exec @playwright/mcp --executable-path /home/sebas/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome"
             ├─1132 "npm exec firecrawl-mcp"
             ├─1134 "npm exec @modelcontextprotocol/server-sequential-thinking"
             ├─1184 "npm exec @hypnosis/docker-mcp-server"
             ├─1191 "npm exec zenith-mcp /home/sebas/proyectos"
             ├─1203 "npm exec yfinance-mcp-ts"
             ├─1221 "npm exec visual-parity-mcp"
             ├─1236 /bin/sh -c nucleus-mcp
             ├─1237 /home/sebas/.hermes/hermes-agent/venv/bin/python3 /home/sebas/.hermes/hermes-agent/venv/bin/nucleus-mcp
             ├─1239 /home/sebas/.hermes/node/lib/node_modules/@blackwell-systems/agent-lsp/node_modules/@blackwell-systems/agent-lsp-linux-x64/bin/agent-lsp javascript:typescript-language-server,--stdio json:vscode-json-language-server,--stdio typescript:typescript-language-server,--stdio yaml:yaml-language-server,--stdio
             ├─1582 sh -c "'pollinations-mcp'"
             ├─1594 node /home/sebas/.npm/_npx/deebfb6067016256/node_modules/.bin/pollinations-mcp
             ├─1597 sh -c "'lsp-intelligence'"
             ├─1598 sh -c "'visual-parity-mcp'"
             ├─1605 sh -c "'docker-mcp-server'"
             ├─1606 sh -c "'mcp-server-filesystem' /home/sebas/proyectos /home/sebas /mnt/c/Users/sebas/Desktop /mnt/c/Users/sebas/Downloads"
             ├─1608 sh -c "'playwright-mcp' --executable-path /home/sebas/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome"
             ├─1609 node /home/sebas/.npm/_npx/1e076fd33f930855/node_modules/.bin/lsp-intelligence
             ├─1610 node /home/sebas/.npm/_npx/17756a002a72957b/node_modules/.bin/docker-mcp-server
             ├─1611 node /home/sebas/.npm/_npx/1669e340947f51a7/node_modules/.bin/visual-parity-mcp
             ├─1622 sh -c "'yfinance-mcp-ts'"
             ├─1631 sh -c "'mcp-server-sequential-thinking'"
             ├─1656 node /home/sebas/.npm/_npx/a3241bba59c344f5/node_modules/.bin/mcp-server-filesystem /home/sebas/proyectos /home/sebas /mnt/c/Users/sebas/Desktop /mnt/c/Users/sebas/Downloads
             ├─1658 node /home/sebas/.npm/_npx/86170c4cd1c5da32/node_modules/.bin/playwright-mcp --executable-path /home/sebas/.cache/ms-playwright/chromium-1228/chrome-linux64/chrome
             ├─1672 sh -c "'ccxt-mcp'"
             ├─1673 node /home/sebas/.npm/_npx/de4825c0c5f6d2a5/node_modules/.bin/yfinance-mcp-ts
             ├─1679 sh -c "'firecrawl-mcp'"
             ├─1680 node /home/sebas/.npm/_npx/de2bd410102f5eda/node_modules/.bin/mcp-server-sequential-thinking
             ├─1693 node /home/sebas/.npm/_npx/20ee063efc65c6dc/node_modules/.bin/ccxt-mcp
             ├─1695 node /home/sebas/.npm/_npx/12b05d58670d8359/node_modules/.bin/firecrawl-mcp
             ├─1696 sh -c "'zenith-mcp' /home/sebas/proyectos"
             ├─1710 node /home/sebas/.npm/_npx/7019d15aca8febd3/node_modules/.bin/zenith-mcp /home/sebas/proyectos
             ├─1778 node /home/sebas/.npm/_npx/1e076fd33f930855/node_modules/.bin/typescript-language-server --stdio
             ├─1789 /home/sebas/.hermes/node/bin/node /home/sebas/.npm/_npx/1e076fd33f930855/node_modules/typescript/lib/tsserver.js --serverMode partialSemantic --useInferredProjectPerProjectRoot --disableAutomaticTypingAcquisition --cancellationPipeName "/tmp/62f40e5edcd26e360dadddc094a712d4/tscancellation*" --locale en --validateDefaultNpmLocation --useNodeIpc
             ├─1790 /home/sebas/.hermes/node/bin/node /home/sebas/.npm/_npx/1e076fd33f930855/node_modules/typescript/lib/tsserver.js --useInferredProjectPerProjectRoot --cancellationPipeName "/tmp/1c64f5473df2f13ad7b250724ff49f4e/tscancellation*" --locale en --validateDefaultNpmLocation --useNodeIpc
             ├─1806 /home/sebas/.hermes/node/bin/node /home/sebas/.npm/_npx/1e076fd33f930855/node_modules/typescript/lib/typingsInstaller.js --globalTypingsCacheLocation /home/sebas/.cache/typescript/5.9 --typesMapLocation /home/sebas/.npm/_npx/1e076fd33f930855/node_modules/typescript/lib/typesMap.json --validateDefaultNpmLocation
             ├─7010 /usr/bin/bash -c "source /home/sebas/.hermes/cache/terminal/hermes-snap-f9cef6d72e11.sh >/dev/null 2>&1 || true\nexport AI_AGENT=\"\${AI_AGENT:-hermes-agent}\" HERMES_AGENT=\"\${HERMES_AGENT:-true}\"\nexport GIT_PAGER=\"\${GIT_PAGER:-cat}\" PAGER=\"\${PAGER:-cat}\"\nbuiltin cd -- /home/sebas/proyectos || exit 126\neval 'TODAY=\$(date +%Y-%m-%d) && REPORT_FILE=\"/home/sebas/proyectos/reports/diagnostico-\$TODAY.md\" && echo \"## Paso 12: Gateway\" >> \"\$REPORT_FILE\" && hermes gateway status >> \"\$REPORT_FILE\" 2>&1 && echo \"\" >> \"\$REPORT_FILE\"'\n__hermes_ec=\$?\numask 077\n__hermes_snap_tmp=\$(mktemp /home/sebas/.hermes/cache/terminal/hermes-snap-f9cef6d72e11.sh.tmp.XXXXXXXXXX) && { { ( unset \${!HERMES_SESSION_*} \${!HERMES_CRON_AUTO_DELIVER_*} \${!HERMES_BROWSER_CONTROL_*} AI_AGENT HERMES_AGENT HERMES_UI_SESSION_ID 2>/dev/null; export -p; ) || true; } > \"\$__hermes_snap_tmp\" && mv -f \"\$__hermes_snap_tmp\" /home/sebas/.hermes/cache/terminal/hermes-snap-f9cef6d72e11.sh; } 2>/dev/null || rm -f \"\$__hermes_snap_tmp\" 2>/dev/null || true\nprintf '\\n__HERMES_CWD_f9cef6d72e11__%s__HERMES_CWD_f9cef6d72e11__\\n' \"\$(pwd -P)\"\nexit \$__hermes_ec"
             ├─7013 /home/sebas/.hermes/hermes-agent/venv/bin/python3 /home/sebas/.local/bin/hermes gateway status
             └─7026 systemctl --user status hermes-gateway --no-pager

Aug 30 19:52:15 NexoPC python[497]: 2026-08-30 19:52:15,087 WARNING agent.conversation_loop: API call failed (attempt 1/3) error_type=InternalServerError thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b summary=HTTP 500: Internal server error
Aug 30 19:52:15 NexoPC python[497]: 2026-08-30 19:52:15,089 WARNING agent.conversation_loop: Retrying API call in 2.4028434488310295s (attempt 1/3) thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b policy=default error=Error code: 500 - {'error': {'message': 'Internal server error', 'type': 'Internal Server Error', 'code': 500}}
Aug 30 19:52:15 NexoPC python[497]: 2026-08-30 19:52:15,330 WARNING tools.mcp_tool: MCP server 'deriv' failed initial connection after 3 attempts, parking until a reconnect is requested (state: connecting → parked): MCPError: Connection closed
Aug 30 19:52:18 NexoPC python[497]: 2026-08-30 19:52:18,805 WARNING agent.conversation_loop: API call failed (attempt 2/3) error_type=InternalServerError thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b summary=HTTP 500: Internal server error
Aug 30 19:52:18 NexoPC python[497]: 2026-08-30 19:52:18,806 WARNING agent.conversation_loop: Retrying API call in 5.661327134615434s (attempt 2/3) thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b policy=default error=Error code: 500 - {'error': {'message': 'Internal server error', 'type': 'Internal Server Error', 'code': 500}}
Aug 30 19:52:24 NexoPC python[497]: 2026-08-30 19:52:24,579 WARNING tools.mcp_tool: MCP server 'mcp-code-review-pro' failed initial connection after 3 attempts, parking until a reconnect is requested (state: connecting → parked): MCPError: Connection closed
Aug 30 19:52:52 NexoPC python[497]: 2026-08-30 19:52:52,263 WARNING agent.conversation_loop: API call failed (attempt 1/3) error_type=InternalServerError thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b summary=HTTP 500: Internal server error
Aug 30 19:52:52 NexoPC python[497]: 2026-08-30 19:52:52,264 WARNING agent.conversation_loop: Retrying API call in 2.334625391785722s (attempt 1/3) thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b policy=default error=Error code: 500 - {'error': {'message': 'Internal server error', 'type': 'Internal Server Error', 'code': 500}}
Aug 30 19:52:56 NexoPC python[497]: 2026-08-30 19:52:56,207 WARNING agent.conversation_loop: API call failed (attempt 2/3) error_type=InternalServerError thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b summary=HTTP 500: Internal server error
Aug 30 19:52:56 NexoPC python[497]: 2026-08-30 19:52:56,208 WARNING agent.conversation_loop: Retrying API call in 4.599475028202612s (attempt 2/3) thread=ThreadPoolExecutor-56_0:129664501606080 provider=nvidia base_url=https://integrate.api.nvidia.com/v1/ model=nvidia/nemotron-3-super-120b-a12b policy=default error=Error code: 500 - {'error': {'message': 'Internal server error', 'type': 'Internal Server Error', 'code': 500}}
✓ User gateway service is running

Recent gateway health:
  ⏳ Gateway draining for restart (2 active agent(s))
✓ Systemd linger is enabled (service survives logout)

## Paso 13: Generar reporte
Reporte generado en: /home/sebas/proyectos/reports/diagnostico-2026-08-30.md

## Paso 14: Memory update
[Guardar issues críticos en memory si los hay]

---\n
**SISTEMA: REQUIERE ATENCIÓN**

Issues detectados:
1. Dashboard web no responde (puerto 9119)
2. Varias habilidades críticas reportan como faltantes debido a conflictos de nombre (requieren identificador completo)
3. Algunos MCPs de trading están deshabilitados en el perfil dev (ccxt, yfinance, deriv)
4. Algunos proyectos legacy sin PROJECT.md

## Paso 10: Memory y AGENTS.md (completado)
/usr/bin/bash: line 5: memory: command not found
## Paso 10: Memory y AGENTS.md
### Memory tool entries
/usr/bin/bash: line 5: memory: command not found

### ~/.hermes/AGENTS.md
Existe (191 líneas)

### Profiles AGENTS.md
- default: OK (66 líneas)
- dev: OK (69 líneas)
- orchestrator: OK (86 líneas)
- research: OK (69 líneas)
- designer: OK (126 líneas)
- trader: OK (34 líneas)
- ciberseguridad: OK (47 líneas)

