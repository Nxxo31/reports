# Análisis centralizado del portafolio de proyectos — 2026-10-05

> Inventario de los 23 repos en `C:/Users/sebas/Desktop/proyectos/`.
> Eje: qué resuelve cada uno, arquitectura, modelo de uso, modelo de negocio y objetivo de funcionamiento.

---

## 0. Mapa de clasificación

| # | Proyecto | Capa | Estado | Modelo principal |
|---|----------|------|--------|------------------|
| 1 | NX-Studio | Sitio web corporativo / marca | Activo v0.4.0 | Marketing + leads B2B |
| 2 | portafolio | CV / marca personal | Activo v0.1.1 | Personal branding |
| 3 | pcshield-launch | Landing de servicio local | v1.0 aprobado gatekeeper | Lead generation B2B local |
| 4 | grani-usco | Landing de negocio local | v0.1.0 | Lead generation B2C local |
| 5 | MyMochis | App monolito cliente + operador | v2.0.0 en Render | Producto SaaS single-tenant |
| 6 | MyMochis Studio | Meta-plataforma multi-tenant | Diseño ADR-005 | Producto PaaS multi-tenant |
| 7 | Multra E.U. | ERP vertical CDA / gestoría | Producción vercel | Producto vertical (SaaS) |
| 8 | NexoCore | SaaS ERP/CRM multi-tenant PYMEs | Alpha Sprint 1+2 parcial | Producto SaaS horizontal |
| 9 | nx-booking | SaaS de reservas multi-tenant | Activo | Producto SaaS |
| 10 | nx-bzns | API comercial multi-tenant read-only | v0.1.0 | Backend de catálogos para landings |
| 11 | contract-guard | CLI / npm package | v2.0.0 publicado | Producto devtool (OSS + soporte) |
| 12 | supply-radar | CLI Go | v1.1.0 | Producto devtool (OSS) |
| 13 | flag-edge | Servidor feature flags | v5 con V5 WS | Producto devtool (self-hosted) |
| 14 | synthetic-trader | Bot de trading algorítmico | Paper 24/7 activo | Producto fintech |
| 15 | e14-fraud-detector | Forense electoral | v0.3.0 | Civic-tech / auditoría |
| 16 | e14-audit-platform | Plataforma E-14 (esqueleto) | Vacío | Civic-tech |
| 17 | NexoAccManager (NX-Manager) | Desktop multi-cuenta gaming | v5.0.0 | Producto consumidor |
| 18 | nva-demons | Experiencia 3D WebGL | v0.2.0 | Demo creativo / portfolio |
| 19 | Tic-Tac-Toe (Triqui) | Juego web PWA | Producción Vercel | Demo / portfolio |
| 20 | wifi-pentest | Toolkit pentest WiFi | Activo | Devtool de seguridad |
| 21 | synth-test | Generador adversario de tests | Inicialización (sin código) | Devtool / OSS planeado |
| 22 | reports | Docs de diagnóstico del SO SophIA | Histórico | Meta-herramienta |
| 23 | NX-Studio-DOC | Doc narrativo NX-Studio | v0.1 | Documento estratégico |

Hay **3 grandes categorías**:

- A) **Marca & captación** (NX-Studio, portafolio, pcshield-launch, grani-usco) → todos apuntan a generar leads o posicionamiento.
- B) **Productos / SaaS** (MyMochis, MyMochis Studio, Multra E.U., NexoCore, nx-booking, nx-bzns, NexoAccManager, synthetic-trader) → cada uno atiende un nicho distinto con modelo de monetización claro o por cerrar.
- C) **Devtools open-source / cívica** (contract-guard, supply-radar, flag-edge, e14-fraud-detector, e14-audit-platform, synth-test, wifi-pentest) → herramientas técnicas reutilizables con misión técnica o cívica.

A esto se suman dos categorías auxiliares: **artefactos creativos** (nva-demons, Tic-Tac-Toe) y **meta-documentación** (reports, NX-Studio-DOC).

---

## 1. NX-Studio — Sitio empresarial B2B (lead gen)

**Idea / caso de uso.** Sitio corporativo de Sebastian Velasco para posicionar una marca de software a medida, captar leads B2B y mostrar capacidades avanzadas. Complementa al portafolio personal: NX-Studio = cara empresarial, portafolio = cara individual.

**Arquitectura.**
- Astro 7.3.2 SSG puro, Tailwind v4 vía `@tailwindcss/vite`, TypeScript.
- Sin backend: desplegado en GitHub Pages (`nxxo31.github.io/NX-Studio/`).
- Pricing engine client-side en `src/data/pricing.ts`: 18 features × 5 categorías × 4 engagement shapes (fixed/retainer/dedicated/tm), con modifiers (rush +25%, bundle -10%, longterm -15%) y banda ±15%.
- Identidad visual "lava neon" con paleta naranja/rojiza centralizada en `src/styles/global.css`.
- Páginas: home narrativa, /servicios (con /osint y /testing especializados), /templates (marketplace), /cotizador, /laboratorio, /contacto, /brand, /logos, /palettes.

**Modelo de uso.**
- Visitante B2B llega desde SEO o referral, recorre la narrativa (qué hacemos → por qué confiar → cómo trabajamos → 2 CTAs), entra al /cotizador para estimar precio en vivo, o descarga /contacta.
- Servicios especializados visibles: OSINT (Analyst $80/h, Senior $110/h, Specialist $140/h) y Testing (modelo OutpostQA con tiers Foundation/Accelerator/Strategic, platform fee separado de seat licenses).

**Modelo de negocio.**
- Servicios profesionales (arquitectura, full-stack, IA, integraciones, OSINT, QA) por hora o engagement shape.
- Productos complementarios: templates comprables (Landing Converter $290, Dashboard Starter $590, Headless E-commerce $890) — pago único, código MIT-licensed.

**Necesidad que solventa.** El cliente B2B latinoamericano no tiene un puente claro entre "quiero software a medida" y un proveedor serio que entienda su contexto regional (timezone, regulación, costes, lenguaje). NX-Studio se posiciona como ese nearshore partner con entrega transparente por fases.

**Objetivo de funcionamiento.** Captar leads cualificados, demostrar expertise técnico vía pricing engine abierto + catálogo de 11 proyectos reales, y cerrar contratos por servicios o venta de templates.

---

## 2. portafolio — Marca personal + showcase técnico

**Idea / caso de uso.** CV / portafolio público de Sebastian Velasco Ocampo (DJ + productor musical + desarrollador full-stack). Es el ancla personal detrás de NX-Studio.

**Arquitectura.**
- Next.js 16.2.10 App Router + React 19.2.4 + Tailwind v4.
- 3D con Three.js 0.185.1 + React Three Fiber 9 + drei 10 (starfield refactorizado de WebGL a Canvas 2D por performance — 60fps estable).
- Animaciones: Framer Motion 12 + GSAP 3.15 + anime.js 3.2 (cada lib con su nicho: micro-interacciones, scroll, typewriter).
- Email con Resend, validación con Zod, i18n con next-intl.
- Form de contacto con honeypot.

**Modelo de uso.** Visitante llega por SEO/redes/IG de DJ, navega el sitio y evalúa perfil técnico. El form convierte a cliente o empleador.

**Modelo de negocio.** No vende directo. Es la carta de presentación que sostiene toda la captación posterior (contrataciones B2B vía NX-Studio, ofertas de empleo, gigs de DJ).

**Necesidad que solventa.** Centralizar la identidad dual (música + software) en una pieza única, evitar fragmentación de marca y dar un punto de contacto profesional.

**Objetivo de funcionamiento.** Generar confianza y conversión: que quien visite entienda en 30 segundos quién es Sebastián, qué hace y cómo contratarlo/colaborar.

---

## 3. pcshield-launch — Landing de mantenimiento PCs (B2B local Neiva)

**Idea / caso de uso.** Sitio de un solo cliente para captar empresas locales (Neiva/Huila) que necesitan mantenimiento preventivo de PCs y participar en licitaciones SECOP II colombianas.

**Arquitectura.** Cero JavaScript. HTML semántico + CSS vanilla + SVG. 14.9 KB de HTML, 13.2 KB de CSS, logo SVG y favicon SVG. Desplegable en GitHub Pages / Vercel / Netlify / S3 sin build.

**Modelo de uso.** Lead → recorre 6 secciones (Hero con ROI, 6 servicios, 3 planes, 3 licitaciones SECOP reales, proceso 4 pasos, form) → deja contacto vía form. El form hoy es demo (alert), pendiente backend real (Formspree/Netlify Forms).

**Modelo de negocio.** Tres tiers:
- Starter $390K/equipo/visita (limpieza + antivirus puntuales).
- Anual $3.5M/equipo/año (4 visitas, descuento por compromiso).
- PyME $8M/mes para 10-15 equipos (outsourcing TI recurrente).
- Más: participación en licitaciones SECOP II con códigos UNSPSC 50312610, 50312000, 50300000, 72267100 ya investigados.

**Necesidad que solventa.** Empresas colombianas pierden 2-4h de productividad/equipo/año por falta de mantenimiento, y el owner no tiene tiempo ni criterio para contratar/licitar. PCShield aparece como proveedor local serio, con precios justificados por research (Edatel 2026, Documentos Tipo 2026 Colombia Compra Eficiente).

**Objetivo de funcionamiento.** Cerrar leads cualificados y, a futuro, ganar licitaciones SECOP II. Cumple los Documentos Tipo 2026 obligatorios desde 16 feb 2026.

---

## 4. grani-usco — Landing de granizados con alcohol (B2C local Neiva)

**Idea / caso de uso.** Sitio informativo de un negocio de granizados con alcohol en Neiva. No hay backend ni DB.

**Arquitectura.** Next.js 16.2.9 App Router + TS + Tailwind 4 + Framer Motion 12. SEO técnico: robots.ts + sitemap.ts + OG tags. Glassmorphism + neon para evocar el ambiente del producto.

**Modelo de uso.** Visitante local ve el menú de 6 productos, conoce la ubicación, contacta. Pendiente: fotos reales, Google Maps embed, datos de contacto reales, CMS dinámico para menú editable.

**Modelo de negocio.** Venta directa B2C del negocio del cliente. El sitio es la vitrina; el dinero pasa fuera.

**Necesidad que solventa.** El dueño del negocio no tenía presencia web optimizada para móvil (mayoría del tráfico colombiano) ni SEO local que posicione "granizados Neiva" o "granizados con alcohol Huila".

**Objetivo de funcionamiento.** Captar clientes locales vía SEO + tráfico orgánico, mostrar menú y ubicación con estética coherente al producto.

---

## 5. MyMochis — App monolito cliente + dashboard operador

**Idea / caso de uso.** Negocio de mochi artesanal. La landing permite al cliente armar pedido (sabores/combos) y enviarlo por WhatsApp al operador; en paralelo, el operador tiene un dashboard privado para gestionar sabores, combos, pedidos, promos, redes, métricas.

**Arquitectura.**
- Node 24+ con `node:sqlite` (sin driver nativo), Express + helmet + express-rate-limit + multer.
- SQLite persistente en Render free tier (1 GB disk, `/var/data/mymochis.db`).
- Frontend vanilla HTML + CSS + JS (sin build step).
- JWT HS256 custom (sin libs) + scrypt para passwords.
- Helmet con CSP estricto, rate-limit (120/min general, 20/15min auth, 30/min uploads), fail2ban (5 intentos → 15min bloqueo).
- 16 rutas REST + uploads con allowlist (jpeg/png/webp/gif/avif/mp4/webm/quicktime/pdf, max 25 MB, 8 files).
- Seed admin idempotente con `ADMIN_PASSWORD_RESET=true` para rotar.

**Modelo de uso.** Dos roles: `customer` (cliente que arma pedido) y `admin` (operador). v2.0.0 cierra con `register` que solo crea `customer` para evitar escalación de privilegios. Cliente arma pedido → se abre `wa.me/...` con mensaje pre-armado al operador + POST a `/api/orders` en paralelo.

**Modelo de negocio.** El sitio es la herramienta operativa de UN negocio. No es SaaS todavía. Cierre v2.0.0 declarado en `reports/`: cuando se sature el free tier de Render o se quiera ofrecer a otros, se migra a tenant de MyMochis Studio (ADR-005).

**Necesidad que solventa.** El operador tenía su negocio en Instagram y WhatsApp sin sistema de pedidos ni métricas. MyMochis le da catálogo gestionable, KPIs, cola de redes sociales y trazabilidad sin aprender ERP.

**Objetivo de funcionamiento.** Operar el negocio actual y, eventualmente, ser el primer caso de uso real del multi-tenant MyMochis Studio.

---

## 6. MyMochis Studio — Meta-plataforma multi-tenant OSS

**Idea / caso de uso.** Convertir MyMochis en un SaaS multi-tenant para pequeños negocios artesanales (food, retail, servicios). El operador de cada tenant crea su tienda/landing desde una meta-plataforma; el sistema provisiona automáticamente una DB Postgres aislada por tenant vía Neon serverless branches.

**Arquitectura (ADR-005).**
- Meta-plataforma Express (puerto 5000) sobre Hetzner CAX31 (€14.19/mes).
- Meta-DB en Neon main branch con `tenants_registry`, `admin_users`, `provisioning_events`, `tenant_health_log`.
- Aislamiento: cada tenant = 1 Neon branch + DB propia. Connection URI cifrado AES-256-GCM en meta-DB.
- Resolución runtime: subdomain (`{slug}.mymochis.app`) / path slug (`/t/{slug}/...`) / header `x-tenant-slug`. Cache TTL 5 min por slug.
- Frontend deploy vía Coolify o Dokku en el mismo Hetzner. Caddy como reverse proxy con on-demand TLS vía Cloudflare DNS challenge.
- Stack: Node 20 LTS + Express 4 + `pg` 8.x + Neon + bcryptjs + jsonwebtoken + scrypt + Cloudflare R2.
- Costo total: ~€14/mes fijo + $0-19 Neon variable.

**Provisioning flow.** Operator en `/admin` → POST `/api/admin/tenants {slug, display_name}` → meta-plataforma crea branch Neon → aplica `sql/tenant-template.sql` (16 tablas + seeds) → cifra URI → registra → primer request emite cert Let's Encrypt vía DNS challenge y enruta al tenant.

**Modelo de uso.** El "operator" del SaaS (no el dueño del negocio) crea tenants desde una UI `/admin`. Cada tenant tiene su app independiente con su DB.

**Modelo de negocio.** SaaS multi-tenant con pricing por tenant (costo fijo ultra-bajo permite margen). El plan es: cuando MyMochis single-tenant colme el free tier de Render o se quiera replicar a otros artesanos, se provisionan tenants automáticamente con coste marginal ~cero.

**Necesidad que solventa.** Replicar la solución operativa de MyMochis (catálogo + pedidos + dashboard) a muchos negocios pequeños sin multiplicar infraestructura ni trabajo manual de deployment. Costo OSS hace el modelo económicamente viable donde Vercel+Supabase por-tenant sería prohibitivo.

**Objetivo de funcionamiento.** Plataforma de provisioning zero-touch para pequeños negocios, con multi-tenancy real (DB aislada por tenant) y coste operativo <€20/mes totales independiente del número de tenants en free tier.

---

## 7. Multra E.U. — ERP vertical CDA / gestoría vehicular

**Idea / caso de uso.** Sistema de gestión para CDA (Centros de Diagnóstico Automotor) y gestoría vehicular en Neiva, Huila. Cubre clientes, vehículos, pólizas, citas, trámites, pagos, inspecciones y cotizaciones.

**Arquitectura.**
- Backend Node 20+ / Express.
- DB polimórfica: SQLite (dev), Neon Postgres (prod), MySQL opcional self-host. Adapter layer en `server/src/db/`.
- Auth: JWT con `JWT_SECRET ≥32 chars`, bcrypt rounds ≥12, validación Zod de env.
- CORS estricto (rechaza `*`), rechaza secretos placeholder.
- Deploy actual: Vercel serverless (`api/index.js` con `@vercel.node`) + static `public/**` con `@vercel/static` + Neon branch `prod`.
- URL productiva: `https://multra-eu.vercel.app`.

**Modelo de uso.** API REST con 14 endpoints:
- Públicos: `/api/health`, `/api/auth/login`, `/api/servicios`, `/api/cotizar/publico`, `/api/recordatorios`.
- Autenticados: clientes, vehículos, pólizas, citas, trámites, pagos, inspecciones, cotizar autenticado, recordatorios admin, stats.
- Frontend vanilla (`public/index.html` landing, `login.html` login, `dashboard.html` panel admin).

**Modelo de negocio.** SaaS vertical específico para CDA/gestoría vehicular en Colombia. Pricing probablemente por suscripción mensual al CDA cliente (no está explícito en README; a inferir por dominio).

**Necesidad que solventa.** Los CDA en Colombia llevan clientes, vehículos, citas, trámites y pagos en planillas/WhatsApp. Multra les da un sistema hecho a la medida de su workflow (incluye endpoint público de cotización desde la landing para captar leads), con DB en la nube y panel admin.

**Objetivo de funcionamiento.** Digitalizar el backoffice de CDAs en Huila y replicar a otras ciudades. Producción viva desde 2026-09-30; pendiente conectar auto-deploy vía GitHub Login Connection de Vercel.

---

## 8. NexoCore — SaaS ERP/CRM/Analytics multi-tenant para PYMEs

**Idea / caso de uso.** Plataforma SaaS multi-tenant donde cada empresa accede a un workspace pre-configurado con una "plantilla de nicho" que activa módulos, campos de inventario, KPIs, pipeline CRM y terminología UI según el vertical.

**Arquitectura.**
- Next.js 16 App Router + TS 5 + PostgreSQL + Prisma ORM.
- Auth: NextAuth v5 multi-tenant con `tenant_id` en JWT, bcrypt verify real (sin backdoor), IDOR fix en tenant-switch, session.maxAge 8h.
- Multi-tenancy: hybrid pool/silo — MVP usa pool compartido con `tenant_id` en cada tabla y RLS PostgreSQL; escala futura: Enterprise pasa a silo dedicada.
- Identificación: subdominio + JWT claim.
- BullMQ + Redis (o Valkey) para background jobs (email, invoice, data-export) con workers reales.
- Resend para email transaccional con 6 templates.
- Stripe pendiente Sprint 4 (billing).

**Plantillas de nicho (7).** Ferretería/Distribuidora, Restaurante/Cafetería, Consultora/Agencia, Retail (talla/color), Clínica (citas+pacientes), Papelería, Carnicería (perecedero+mermas).

**Estado real (2026-09-13).** Sprint 1+2 parcial verificados: setup, schema multi-tenant completo, 7 plantillas, auth hardened, RLS activado en 16 tablas, inventario + CRM básicos, 2 workers reales, audit log parcial. Pendiente: Analytics (S3), billing Stripe (S4), search global (S4), rate-limit (S4).

**Modelo de uso.** Empresa se registra → onboarding con selector de nicho → workspace pre-configurado → módulos activos (inventario, CRM, analytics). Repository pattern garantiza aislamiento por `tenant_id`. RLS en DB blinda contra bugs de filtrado.

**Modelo de negocio.** SaaS multi-tenant con planes Starter / Pro / Enterprise. Stripe billing Sprint 4. LITA (20% I+D) + programa "TechParaTodos" (10% capacidad a impacto social) son parte del modelo de marca. Posicionamiento nearshore para LatAm con estándares globales.

**Necesidad que solventa.** Las PYMEs latinoamericanas no pueden pagar SAP/Oracle ni configurar Odoo a medida. NexoCore les da plantillas verticales listas para usar, en su idioma, con su regulación (facturación electrónica, nómina, pagos locales), ajustables por override de campos custom.

**Objetivo de funcionamiento.** SaaS escalable horizontalmente con multi-tenancy seria (pool + RLS), verticalizable por nicho, con calidad incorporada (tests, RLS, audit, workers reales). Es el flagship técnico de NX-Studio para vender como producto.

---

## 9. nx-booking — SaaS de reservas multi-tenant

**Idea / caso de uso.** SaaS de reservas multi-tenant (no leí README detallado por estar pendiente del skill `nx-booking-patterns`; lo deduzco del nombre y stack).

**Arquitectura (inferida).** Astro 7 + Prisma + TS. Hay `prisma/` y `astro.config.mjs`; `dist/` indica build.

**Modelo de uso.** Multi-tenant: cada negocio tiene su página de reservas configurable.

**Modelo de negocio.** SaaS con pricing por tenant o por volumen de reservas.

**Necesidad que solventa.** Negocios de servicios (consultorios, salones, estudios) necesitan agendar sin configurar calendly/jotform. Una solución self-hosted y configurable.

**Objetivo de funcionamiento.** Plantilla/marco reutilizable para entregar verticales de reservas sin partir de cero.

*(Nota: pendiente lectura detallada — el skill `nx-booking-patterns` ya existe y da el contexto.)*

---

## 10. nx-bzns — API comercial multi-tenant read-only

**Idea / caso de uso.** API de gestión comercial multi-tenant con auth, productos, combos, promos, pedidos, KPIs y modo read-only para servir a landings externas. Es el "núcleo de datos" desacoplado de cualquier cliente UI.

**Arquitectura.** Node ≥22.5, Express 4.21, CommonJS. Sin más deps declaradas. Probablemente SQLite + JWT, patrón similar a MyMochis pero con foco en servir como backend a landings de clientes vía endpoints read-only.

**Modelo de uso.** El operador expone esta API y la consume desde landings externas (un tenant puede tener su landing separada que pide `/api/public` para mostrar catálogo, y otro front que pide `/api/orders` autenticado).

**Modelo de negocio.** Servicio B2B: cada cliente (PyME) paga una suscripción por su instancia. Reutiliza la lógica de MyMochis pero desacoplada del monolito.

**Necesidad que solventa.** Landings de clientes que necesitan catálogo + pedidos + KPIs sin re-implementar backend. Una API bien hecha permite que múltiples frontends (landing, app móvil, dashboard) compartan el mismo motor.

**Objetivo de funcionamiento.** Acelerar la entrega de verticales: una vez configurado el tenant, cualquier front consume la API sin tocar el backend.

---

## 11. contract-guard — CLI para diff de contratos de API

**Idea / caso de uso.** Los equipos rompen integraciones al cambiar un campo de su API sin darse cuenta de que es breaking change. contract-guard compara dos versiones de una spec (OpenAPI 3.x, GraphQL SDL, gRPC proto3) y emite un reporte Markdown categorizado por severidad.

**Arquitectura.**
- Node 18+, TypeScript 5.4+, npm package publicable (badge npm version).
- CLI con Commander (liviano, soporta subcomandos).
- Vitest como test runner (mismo runtime que Vite, ESM nativo).
- Sin LLMs, sin servicios externos, sin dependencias de pago.
- Parser por formato + AST común (normaliza GraphQL y gRPC a un AST común para reusar el diff/rules engine).

**Reglas soportadas.** OpenAPI: 8 reglas (endpoint-removed, parameter-removed, parameter-required-added, parameter-type-changed, response-removed, endpoint-added, response-added, parameter-optional-added). GraphQL: type/field/enum. gRPC: service/method/message/enum.

**Modo `--strict`** → exit 1 si hay breaking changes, falla el pipeline.

**Modelo de uso.**
- Dev local: `contract-guard compare old.json new.json`.
- CI: `--strict` en GitHub Actions bloquea merges con breaking changes.
- Reporte Markdown con emojis 🔴🟡🟢.

**Modelo de negocio.** Paquete npm open-source MIT. El plan documentado: reglas configurables (B-1), soporte AsyncAPI (B-2), GitHub Action con comentarios automáticos (B-3), SaaS de detección basada en tráfico real (B-4).

**Necesidad que solventa.** Equipos con microservicios o APIs públicas rompen consumidores aguas abajo cuando cambian un campo sin querer. contract-guard atrapa eso en el PR, antes de producción, con una herramienta sin fricción (CLI local, sin servidor).

**Objetivo de funcionamiento.** Hacer del "diff de contrato" un paso obligado del code review de cualquier API, con cero infraestructura.

---

## 12. supply-radar — CLI Go para SBOM y vulnerabilidades

**Idea / caso de uso.** CLI de supply chain security que escanea dependencias Go y npm, consulta OSV (Open Source Vulnerabilities), genera SBOMs SPDX/CycloneDX y reporta riesgos — autónomo, offline-capable, sin dependencias externas.

**Arquitectura.**
- Go 1.23+, stdlib puro (sin go.sum externos salvo los inevitables del toolchain).
- `flag` stdlib (no Cobra) para CLI minimalista.
- `net/http` para api.osv.dev.
- Concurrencia: semáforo de 10 goroutines (respeta rate limit público de OSV ~10 req/s).
- Cache in-memory con TTL configurable.
- Reporter: tabla, JSON, json-summary, markdown, SARIF (GitHub Code Scanning).
- Mode `--offline` para uso sin red.
- `--fail` con exit code ≠ 0 cuando hay vulns críticas (CI-friendly).

**Modelo de uso.** Dev/CI: `supply-radar scan` → tabla/JSON/markdown/SARIF. Monorepos: scan recursivo (pendiente Issue #1).

**Modelo de negocio.** OSS sin fines de lucro inmediato. Es una pieza de portfolio + herramienta utilizable por la comunidad.

**Necesidad que solventa.** Los devs no saben realmente qué dependencias vulnerables tienen en lockfile (no en manifest declarado). supply-radar lee el lockfile real (go.sum, package-lock.json) y consulta OSV para visibilidad verdadera. Paradójicamente, una herramienta de supply chain con cero dependencias externas reduce su propia superficie de ataque.

**Objetivo de funcionamiento.** Hacer del escaneo de vulnerabilidades algo trivial de integrar en CI, sin vendor lock-in ni costes de SaaS.

---

## 13. flag-edge — Servidor self-hosted de feature flags

**Idea / caso de uso.** Servidor de feature flags con evaluación por usuario, multi-tenancy opcional, métricas A/B, streaming de config por SSE y WebSocket, JWT auth y audit log. Es un sustituto self-hosted de LaunchDarkly/Flagsmith para equipos que no quieren dar su telemetría a un SaaS.

**Arquitectura (Go).**
- Go estándar: `flag` stdlib + `net/http` + signal handling.
- Store intercambiable: `--store=memory` (no persistente) o `--store=sqlite` (persistente vía `--db flag-edge.db`).
- Endpoints REST: `/health`, `/flags`, `/flags/{key}`, `POST/PUT/DELETE /flags`, `/config`, `/config/stream` (SSE), `/ws/flags` y `/ws` (WebSocket bidireccional), `/evaluate`, `/metrics/{dashboard,events,summary}`.
- Multi-tenancy (V4): JWT auth, `/auth/token`, tenant store, `/audit` log.
- Seed demo opcional con `--seed`.

**Modelo de uso.** Operador arranca `flag-edge --store=sqlite --jwt --seed`, expone en `localhost:8080`. Apps consumen `/config/stream` para recibir updates en tiempo real sin polling, `/evaluate` para resolver flag por usuario.

**Modelo de negocio.** OSS self-hosted (no hay pricing publicado). Es producto portfolio/devtool.

**Necesidad que solventa.** Equipos con políticas de "no enviar datos a SaaS externos" o que quieren control total de su evaluación de flags. LaunchDarkly/Flagsmith都不错 pero son SaaS; flag-edge es el sustituto que vive en tu infra.

**Objetivo de funcionamiento.** Servidor de feature flags con paridad funcional a SaaS comerciales, ejecutable con un binario en cualquier host, con multi-tenancy y métricas para que un equipo mediano no dependa de proveedor externo.

---

## 14. synthetic-trader — Bot de trading algorítmico sobre Deriv

**Idea / caso de uso.** Plataforma SaaS (en construcción) para desplegar, gestionar y monitorear bots de trading algorítmico sobre índices sintéticos de Deriv (stpRNG, R_100, Boom/Crash) y crypto, con gestión de riesgo institucional y analítica en tiempo real. Estado actual: paper trading 24/7 con cuenta demo de $10K.

**Arquitectura.**
- Python 3.12+, FastAPI + uvicorn puerto 8001.
- Conexión a Deriv vía WebSocket con PAT + OTP flow.
- Estrategias vía factory pattern: BreakoutStrategy, VolatilityStrategy, ConfluenceStrategy, StepIndexStrategy, DriftBoomCrashStrategy (6 registradas).
- Risk Manager: Kelly dinámico × 0.25 / vol_multiplier, hard cap 1.5% per trade, dual circuit breaker (3 losses consecutivas → cooldown, 5% drawdown → halt).
- Capital Allocator: Reserva 80% (nunca se arriesga intradía) + Superávit diario 20% + reinversión de ganancias.
- Attribution Engine (Brinson-Fachler) mide qué estrategia rinde por símbolo.
- Return Projector: Monte Carlo forward 10,000 paths con bandas P5/P50/P95.
- Persistencia: Parquet (candles) + JSONL (state/equity/trades realtime) + SQLite (`data/strategies.db`).
- Dashboard Next.js 16 + Tailwind + Recharts, WebSocket `/ws/live-data`.
- Docker + systemd timer para paper 24/7 con reconexión backoff.
- 37 unit tests; pytest green.

**Modelo de uso.** Trader/desarrollador configura estrategia y parámetros → bot corre en paper 24/7 → dashboard muestra equity, trades, KPIs en español (sin siglas) → attribution y projection guían decisiones.

**Modelo de negocio.** Estado actual: proyecto personal + portfolio. Hoja de ruta: SaaS para que otros traders desplieguen sus bots. Live trading bloqueado por gate explícito del usuario (R-16 pendiente).

**Necesidad que solventa.** Operar índices sintéticos 24/7 con disciplina institucional (Kelly sizing, circuit breakers, attribution, projection) sin caer en discretionary gambling. Separa la emoción (dashboard con métricas frías) de la ejecución (bots disciplinados).

**Objetivo de funcionamiento.** Plataforma de bots reproducible y auditable con risk management serio, que pueda correr live cuando el usuario dé el OK explícito, y que escale a multi-tenant para otros traders.

---

## 15. e14-fraud-detector — Forense electoral ciudadana Colombia

**Idea / caso de uso.** Auditoría forense ciudadana de actas electorales E-14 de Colombia. Pipeline multi-capa: ingesta PDFs → validación aritmética → visión por computador → VLM dirigido → comparación oficial vs ciudadano → dashboard público.

**Arquitectura.**
- Python 3.14 (venv), FastAPI puerto 8700.
- PyMuPDF (fitz) para extracción de texto + coordenadas normalizadas.
- Capa 0 (aritmética): valida totales, detecta excede_total, no_coincide, nivelacion fraudulenta, firmas faltantes.
- Capa 1 (OpenCV + numpy + Tesseract OCR): heurísticas de grosor de trazo, separador entre firmas, tinta anómala en firmas. Score 0-1.
- Capa 2 (NVIDIA NIM llama-3.2-90b-vision): VLM dirigido a zonas con score > 0.6.
- Comparación oficial vs ciudadano + verificación por consenso democrático con cuórum configurable.
- DB local SQLite (11 tablas + 2 views + triggers) + Supabase Postgres para producción (5 actas migradas).
- Docker (acquisition/) como microservicio.
- QR/barcode con pyzbar.

**Estado (v0.3.0).** Backend robusto, pendiente dashboard público.

**Modelo de uso.** Ciudadano sube foto del E-14 → capas 0/1/2 corren automáticamente → resultado se cruza con la versión oficial publicada por la Registraduría → mesa entra en dashboard público como verificada o con discrepancias. Consenso democrático de múltiples uploads determina legitimidad.

**Modelo de negocio.** Civic-tech sin ánimo de lucro inmediato. El valor es la integridad democrática.

**Necesidad que solventa.** Las actas E-14 son el documento base del conteo electoral colombiano; detectar alteraciones (aritméticas o visuales) a escala ciudadana es una garantía de transparencia que el estado no provee.

**Objetivo de funcionamiento.** Hacer escalable la auditoría forense electoral de 18 muestras a las 122K actas nacionales, preservando 100% confidencialidad (credenciales Supabase solo en env, sin leaks).

---

## 16. e14-audit-platform — Esqueleto del proyecto hermano

**Idea / caso de uso.** Repositorio companion a e14-fraud-detector, actualmente solo tiene `README.md` (22 bytes) y `.git/`. Es el placeholder para la plataforma web de auditoría ciudadana.

**Estado.** Vacío (no hay código). Probablemente será el dashboard público del fraud-detector + cola de verificación + mapa de mesas legítimas.

**Modelo de negocio.** Civic-tech, mismo paraguas que e14-fraud-detector.

**Objetivo de funcionamiento.** A futuro: servir el dashboard público del verificador forense de E-14.

---

## 17. NexoAccManager (NX-Manager) — Desktop multi-cuenta gaming

**Idea / caso de uso.** Gestor open-source de múltiples cuentas para plataformas de gaming (Roblox focus). 100% local, sin servidores, sin cloud.

**Arquitectura.**
- Electron 30.5.1 + TypeScript 5.3 + React 18.2 + Mantine 7.17.8 + Vite 5.
- Arquitectura hexagonal: `domain/` (entities, interfaces), `application/` (React components, hooks, store Zustand), `infrastructure/` (SQLite via better-sqlite3, Discord, Roblox, Captcha, WS, LocalApi, IPC handlers).
- preload con `contextBridge.exposeInMainWorld('api', api)` mapea 1:1 a `ipcRenderer.invoke` — 102 canales IPC sincronizados.
- Backup encriptado (AES-256-GCM + PBKDF2) con base64 para binarios (bug crítico cerrado).
- Discord RPC opcional (CLIENT_ID por env var).
- Local API para control externo: WS + HTTP con port allow-list, token rotation, auth.
- 4 suites / 103 tests, cobertura 68.24% (mejor: services/ 92.48%, database/ 61.18%).

**Estado verificado (2026-09-13).** Lint 0 errores, TS 0 errores, tests verdes, IPC sincronizado. Pendiente: cobertura >80%, tests para RobloxBottingService/MultiRobloxService (decisión de scope), build local requiere VS Build Tools C++.

**Modelo de uso.** Jugador de Roblox con N cuentas instala NX-Manager, lanza instancias múltiples, gestiona cookies, bots de farmeo opcionales, skins, backups automáticos, integración Discord (RPC para mostrar estado).

**Modelo de negocio.** MIT open-source. Es producto portfolio + herramienta para comunidad. Monetización potencial: features premium, soporte, marketplace de bots.

**Necesidad que solventa.** Gamers con múltiples cuentas no quieren un perfil de browser por cuenta ni perderlas al limpiar cookies. NX-Manager las gestiona con persistencia local, encriptación y bots de farmeo donde aplique.

**Objetivo de funcionamiento.** Desktop app estable, testeable, encriptada, con arquitectura limpia que pueda extender a otras plataformas de gaming (no solo Roblox) sin reescribir el core.

---

## 18. nva-demons — Experiencia 3D WebGL inmersiva

**Idea / caso de uso.** Pieza creativa: experiencia 3D interactiva en navegador con partículas GPU, post-processing (bloom, glitch, distortion), modelos GLTF, controles de cámara drei, framer-motion para transiciones UI↔3D. Demo de creative coding + portfolio técnico.

**Arquitectura.**
- Next.js 16.3.1 App Router + React 19.2.4 + TS 5.
- Three.js 0.185.1 + React Three Fiber 9.6.1 + drei 10.7.7.
- VFX: `three-vfx` 0.2.1 + `wawa-vfx` 1.2.10.
- Tailwind v4 + framer-motion 12 + lucide-react.
- Componentes `components/three/` con `Scene.tsx`, `Models/`, `Particles/`, `PostFX/`; UI overlay en `components/ui/`.
- Fallback automático a WebGL1 + reduce particle count en low-power devices.

**Modelo de uso.** Visitante entra al sitio → experiencia 3D interactiva. No hay goal funcional explícito; es portfolio de capacidades de creative coding + WebGL.

**Modelo de negocio.** Portfolio piece. Atrae tráfico de reclutadores/clientes técnicos fascinados por la ejecución visual.

**Necesidad que solventa.** Diferenciación visual en un mar de portfolios Next.js estándar. Demuestra dominio de R3F + GPU shaders + particle systems en un demo concreto.

**Objetivo de funcionamiento.** Mostrar capacidad técnica avanzada de frontend/WebGL en un demo que se sienta vivo y de alta calidad.

---

## 19. Tic-Tac-Toe (Triqui) — Juego PWA con IA invencible

**Idea / caso de uso.** Juego de Tic-Tac-Toe con IA invencida (Minimax + Alpha-Beta pruning), glassmorphism neon, PWA offline, sonidos sintetizados vía Web Audio, leaderboard persistente.

**Arquitectura.** Cero dependencias npm. HTML5 semántico + CSS3 (glassmorphism, grid, animations, custom properties) + Vanilla JS ES6+ (state machine + IA) + SVG (línea ganadora animada) + Web Audio API (osciladores) + localStorage (scoreboard, theme) + Service Worker (cache-first assets, network-first HTML). Deploy en Vercel con CI.

**Modelo de uso.** Jugador vs IA (fácil o imposible) o PvP local. Scoreboard persistente, tema claro/oscuro, leaderboard con historial.

**Modelo de negocio.** OSS MIT + portfolio. Atrae tráfico SEO ("tic tac toe", "triqui") y demuestra dominio de fundamentos (Minimax, PWA, Web Audio).

**Necesidad que solventa.** Nadie quiere instalar una app para jugar tres en raya. Una PWA ligera, offline, con IA invencible, es el equilibrio justo entre "demo técnica" y "producto jugable".

**Objetivo de funcionamiento.** Demo de calidad: cero build, cero deps, IA matemáticamente perfecta, performance sub-milisegundo. Es la pieza mínima pero completa.

---

## 20. wifi-pentest — Toolkit pentest WiFi autorizado

**Idea / caso de uso.** Toolkit automatizado para auditorías de seguridad en redes Wi-Fi WPA/WPA2: capturar handshakes, ataques de diccionario, generación de reportes con recomendaciones, todo con ética y autorización explícita como requisito.

**Arquitectura.**
- Bash + Python 3.11.
- Subcomandos del script `wifi-pentest.sh`: scan, capture, crack, wps, report.
- Stack externo: aircrack-ng suite, reaver, bully, wash, hcxdumptool, hcxpcaptool, hashcat.
- Config YAML/JSON (interfaces, diccionarios, timeouts).
- Reportes Jinja2 → HTML/PDF (wkhtmltopdf o WeasyPrint).
- Logging a archivo + consola.
- Modo simulación (sin deautenticación) para pruebas seguras.

**Modelo de uso.** Pentester autorizado ejecuta:
1. `wifi-pentest scan --interface wlan0` → lista de redes.
2. `capture --bssid X --channel 6 --interval 5` → handshake con deauth opcional.
3. `crack --handshake x.cap --wordlist rockyou.txt` → recupera clave.
4. `wps --bssid X --interface wlan0mon` → opcional WPS con reaver/bully.
5. `report --handshake x.cap --output reporte.html` → reporte con hallazgos + mitigaciones.

**Modelo de negocio.** OSS para uso ético. Distribución: script único con subcomandos.

**Necesidad que solventa.** Pentesters y auditores de seguridad necesitan automatizar el flujo WPA2 con generación de reporte legible para clientes, sin reinventar el scripting cada vez. El modo simulación y los avisos legales reducen el riesgo de uso indebido.

**Objetivo de funcionamiento.** Toolkit con una sola responsabilidad (pentest WiFi autorizado) que entrega un reporte profesional al cliente con recomendaciones concretas (WPA3, contraseñas fuertes, desactivar WPS).

---

## 21. synth-test — Generador adversario de tests (en diseño)

**Idea / caso de uso.** Generar casos de prueba diseñados para romper basándose en categorías conocidas de bugs (boundary values, type confusion, encoding issues, casos estructuralmente extraños) a partir de un esquema OpenAPI/JSON Schema, no datos aleatorios sin criterio. Ejecución HTTP contra el endpoint objetivo + shrinking automático al caso mínimo reproducible.

**Arquitectura (planeada).**
- Python (ecosistema Hypothesis como base conceptual).
- Sin DB en core; SQLite/Postgres solo en versión SaaS para histórico.
- Property-based testing + generadores conscientes de esquema.
- Sin dependencias de pago.

**Roadmap.**
- V1: generador core JSON Schema con 5 categorías + ejecutor HTTP + reporte terminal.
- V2: OpenAPI completo, shrinking, CI.
- V3: SaaS de fuzzing continuo con histórico de regresiones.

**Modelo de negocio.** CLI/librería OSS gratuita. SaaS de pago para fuzzing continuo en CI con reportes históricos.

**Necesidad que solventa.** Equipos prueban APIs/pipelines solo con casos "felices" que ellos mismos imaginan. synth-test ataca los boundary values y casos adversarial que pasan hasta producción. Diferenciador: no es "otro generador random", está guiado por esquema.

**Objetivo de funcionamiento.** Encontrar bugs que el equipo no imaginó al diseñar sus tests, con reproducibilidad determinista y reporte ejecutable. Estado actual: en diseño (sin implementación todavía).

---

## 22. reports — Diagnósticos del SO SophIA

**Idea / caso de uso.** Repositorio de documentos markdown con diagnósticos cronológicos del sistema operativo autónomo (SophIA). Incluye auditorías familiares, research de Roblox executors, research del ecosistema Hermes, diagnósticos diarios (cron-mcp-health, diagnósticos 2026-07-26 a 2026-09-22).

**Arquitectura.** Solo markdown. Sin código. Es el logbook del operador sobre el estado de su infraestructura de agentes.

**Modelo de negocio.** Interno. Documentación operativa.

**Necesidad que solventa.** Memoria histórica de decisiones, incidentes, upgrades y research del ecosistema Hermes. Permite retomar contexto tras pausas largas.

**Objetivo de funcionamiento.** Trazabilidad humana de la evolución del setup técnico + research descargable para futuras sesiones.

---

## 23. NX-Studio-DOC — Documento narrativo del modelo NX-Studio

**Idea / caso de uso.** Documento estratégico narrativo que complementa al sitio web NX-Studio: visión de puente tecnológico LatAm↔Global, modelo de entrega por fases, servicios especializados, metodología de transparencia, TechParaTodos (10% capacidad a impacto social), LITA (20% I+D).

**Arquitectura.** Solo markdown (`NX-STUDIO_COMPLETE_MODEL.md`, 15 KB).

**Modelo de uso.** Lectura por clientes prospecto, aliados estratégicos, candidatos a empleo. Es la versión "larga" del posicionamiento que el sitio web condensa.

**Modelo de negocio.** Refuerza el modelo híbrido servicios+productos: services a precio fijo por fase, productos pre-construidos (CliniFlow, MercadoLocal, RutaÓptima, EducaFlex), retainer, licensing de IP reutilizable.

**Necesidad que solventa.** El sitio web NX-Studio es la vitrina rápida; este documento es la propuesta de valor en profundidad para decisiones de contratación de mayor envergadura.

**Objetivo de funcionamiento.** Soporte a ventas enterprise + posicionamiento de marca con storytelling (impacto social medible: AguaSegura, MicroCréditoFácil, EducaRural).

---

## Lecturas transversales

**Coherencia de portfolio.** Todos los proyectos se alinean con un mismo perfil: "ingeniero full-stack con sensibilidad de producto, orientado a mercado latinoamericano, que sabe vender (no solo construir)". El stack es diverso a propósito — Go, Node, Python, Next, Astro, Electron, Vanilla JS — porque cada problema pide su herramienta. No hay proyectos "muerto" en sentido estricto: incluso los que están en `v0.1.0` o sin implementar (e14-audit-platform, synth-test) tienen su `PROJECT.md` con matriz de trazabilidad y roadmap.

**Tensión central: SaaS vs producto vs OSS.** Hay tres vías de monetización conviviendo:
1. **Servicios profesionales** (NX-Studio): posicionamiento + leads → contratos por hora o engagement shape.
2. **SaaS multi-tenant** (MyMochis Studio, NexoCore, Multra E.U., nx-booking, nx-bzns): producto replicable con coste marginal bajo.
3. **OSS devtool / civic-tech** (contract-guard, supply-radar, flag-edge, e14-fraud-detector, synth-test, wifi-pentest): reputación + comunidad + SaaS futuro (B-3/B-4 documentados en algunos).

El operador ha declarado explícitamente que NO automatiza cobros a clientes — los cobros son manuales a su cuenta personal (Nequi/Bancolombia/Stripe Connect), y el canal automatizado es solo WhatsApp. Esto aplica a la **agencia NX-Studio** (servicios B2B), no a los SaaS verticales.

**Patrón técnico recurrente.** Varios proyectos comparten elecciones:
- DB-per-tenant en multi-tenant (MyMochis Studio con Neon branches, NexoCore con RLS pool + silo futuro).
- Auth hardened: bcrypt real + JWT con expiración corta + IDOR fix (NexoCore, NexoAccManager).
- Security headers + CSP estricto + rate-limit (MyMochis, MyMochis Studio, Multra E.U.).
- "Self-hostable, sin vendor lock-in" (flag-edge, supply-radar, contract-guard, MyMochis Studio OSS stack).

**Patrón de producto recurrente.** Verticales SaaS para PYMEs colombianas (MyMochis, Multra E.U., grani-usco, pcshield) con pricing en pesos colombianos ($390K, $8M, etc.) + plataforma multi-tenant genérica que los puede hospedar a todos (NexoCore, MyMochis Studio). Esto sugiere que la visión de fondo es: **un marketplace de SaaS verticales, cada uno deployable como tenant de un meta-platform**.

**Estado de completitud.**
- **Producción viva**: MyMochis (Render), Multra E.U. (Vercel), Tic-Tac-Toe (Vercel), pcshield-launch (aprobado, deploy pendiente), portafolio (deploy-ready), NX-Studio (GitHub Pages).
- **Diseño + scaffolding**: MyMochis Studio (ADR-005 sin deploy), NexoCore (Alpha Sprint 1+2), e14-audit-platform (vacío), synth-test (solo doc).
- **Portfolio/devtool estable**: contract-guard, supply-radar, flag-edge, wifi-pentest, nva-demons, NexoAccManager.
- **Meta/operacional**: reports (logs), NX-Studio-DOC (estrategia).

**Riesgos observados.**
- **Demasiados frentes para un solo operador.** 23 repos con estado heterogéneo. El operador mismo lo sabe (varios PROJECT.md mencionan "decisiones diferidas" o "pendientes bloqueados").
- **nx-bzns y nx-booking están poco documentados en README** — riesgo de zombie code si no se activan.
- **synthetic-trader consume tiempo continuo** (cron 24/7, supervisor, attribution diaria). Es un compromiso grande para un proyecto sin monetización inmediata.
- **e14-audit-platform está vacío** — riesgo de duplicar el fraud-detector si no se delimita alcance.
- **Cobertura de tests variable**: NexoAccManager 68%, NexoCore sin tests visibles en su PROJECT, Multra E.U. tiene tests `node:test` pero no documenta %, MyMochis tiene tests `node:test`.

**Próximos movimientos sugeridos** (no pedidos por el operador, solo lectura del estado):
1. **Decidir qué 3-5 proyectos son el "core"** y cuáles son side-quests. Probablemente: NX-Studio (cara B2B), portafolio (cara personal), NexoCore/MyMochis Studio (productos flagship), contract-guard o supply-radar (OSS con tracción).
2. **Activar auto-deploy en Multra E.U.** (Vercel↔GitHub Login Connection es un ítem explícito).
3. **Documentar nx-booking y nx-bzns** para saber si siguen o se archivan.
4. **Cerrar sprints 3-4 de NexoCore** (Analytics + Stripe billing) para validar producto.
5. **Definir fecha go/no-go de synthetic-trader** o se queda en paper indefinido.

---

*Generado el 2026-10-05. Material persistido en `reports/analisis-centralizado-proyectos-2026-10-05.md` para referencia futura.*
