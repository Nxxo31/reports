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
| Base de datos | SQLite (desarrollo) / PostgreSQL (producción) | latest | Almacenamiento de metadatos de informes |
| Almacenamiento de archivos | Sistema de archivos local (desarrollo) / AWS S3 (producción) | latest | Guardar archivos markdown y recursos asociados |
| API | API Routes de Next.js | — | Endpoints para CRUD de informes, búsqueda, exportación |
| Autenticación | NextAuth.js (opcional) | latest | Protección de rutas de administración |
| Exportación | Pandoc + wkhtmltopdf (via scripts) | latest | Conversión de markdown a PDF/HTML |
| Hosting | Vercel (frontend) + Vercel Serverless Functions (API) o Docker (si se prefiere) | — | Deployment sencillo y escalable |
| Herramientas de código | ESLint, Prettier, TypeScript strict, husky + lint-staged | — | Calidad de código |

### Diagrama de Arquitectura (simplificado)

```
┌──────────────────────────────────────────────────────────────┐
│                    CAPA CLIENTE (Next.js)                      │
│                                                              │
│  app/page.tsx (lista de informes)                              │
│   ├─ <Header>                                                  │
│   ├─ <SearchBar>                                               │
│   ├─ <FilterPanel> (etiquetas, fechas)                         │
│   ├─ <ReportList> (tarjetas con resumen)                       │
│   └─ <Pagination>                                              │
│                                                              │
│  app/[id]/page.tsx (detalle del informe)                       │
│   ├─ <ReportHeader> (título, autor, fecha, etiquetas)          │
│   ├─ <ReportContent> (markdown renderizado)                    │
│   ├─ <Attachments> (si los hay)                                │
│   └─ <ActionButtons> (editar, descargar PDF, compartir)        │
│                                                              │
│  app/new/page.tsx (formulario de creación)                     │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│                    CAPA API (API Routes)                       │
│                                                              │
│  GET   /api/reports    → listado paginado, filtrado, buscado  │
│  GET   /api/reports/:id → obtener informe por ID              │
│  POST  /api/reports    → crear nuevo informe (markdown)        │
│  PUT   /api/reports/:id → actualizar informe                  │
│  DELETE /api/reports/:id → eliminar informe                   │
│  GET   /api/reports/:id/export?format=pdf → exportar a PDF     │
│  GET   /api/reports/:id/export?format=html → exportar a HTML   │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│                    CAPA ALMACENAMIENTO                         │
│                                                              │
│  /reports/                                                     │
│     ├─ 2026-07-20-familia-villaneda-audit.md                  │
│     ├─ 2026-07-26-roblox-executor-research.md                 │
│     └─ ...                                                    │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│                    CAPA BASE DE DATOS                          │
│                                                              │
│  Tabla: reports                                                │
│    - id (uuid)                                                 │
│    - title                                                     │
│    - slug                                                      │
│    - author                                                    │
│    - date                                                      │
│    - tags (array)                                              │
│    - severity                                                  │
│    - file_path                                                 │
│    - created_at                                                │
│    - updated_at                                                │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

### Flujo de Datos (Creación de un informe)

```
[Usuario navega a /new]
  → [Muestra formulario: título, autor, fecha, tags, severidad, contenido markdown]
  → [Usuario envía formulario]
    → [POST /api/reports]
      → [Validar datos con Zod]
      → [Guardar markdown en /reports/ con nombre basado en fecha y slug]
      → [Insertar metadatos en base de datos]
      → [Redirigir a /reports/[id]]
  → [Página de detalle muestra el informe renderizado]
```

### Flujo de Datos (Exportación a PDF)

```
[Usuario hace clic en "Descargar PDF" en /reports/[id]]
  → [GET /api/reports/:id/export?format=pdf]
    → [Leer archivo markdown desde /reports/]
    → [Convertir markdown a HTML (usando marked o similar)]
    → [Convertir HTML a PDF (usando wkhtmltopdf o puppeteer)]
    → [Devolver PDF como descarga]
```

---
## 📊 Matriz de Trazabilidad (Requisitos Funcionales)

| Req ID | Descripción | Componente | Estado | Verificación |
|--------|-------------|------------|--------|--------------|
| R-01 | Listado de informes con paginación y búsqueda | `app/page.tsx` | ⏳ pendiente | UI muestra lista, paginación funciona |
| R-02 | Filtro por etiquetas y rango de fechas | `<FilterPanel>` | ⏳ pendiente | Filtros aplican correctamente |
| R-03 | Búsqueda full-text por título y contenido | API `/api/reports` con búsqueda | ⏳ pendiente | Resultados coinciden con consulta |
| R-04 | Página de detalle de informe con renderizado markdown | `app/[id]/page.tsx` | ⏳ pendiente | Markdown se renderiza correctamente |
| R-05 | Formulario para crear nuevo informe | `app/new/page.tsx` | ⏳ pendiente | Envío exitoso crea nuevo registro |
| R-06 | Edición de informe existente | `app/[id]/edit/page.tsx` (opcional) | ⏳ pendiente | Cambios se guardan y reflejan en detalle |
| R-07 | Eliminación de informe (con confirmación) | DELETE `/api/reports/:id` | ⏳ pendiente | Informe eliminado de DB y sistema de archivos |
| R-08 | Exportación a PDF | API `/api/reports/:id/export?format=pdf` | ⏳ pendiente | PDF generado y descargable |
| R-09 | Exportación a HTML | API `/api/reports/:id/export?format=html` | ⏳ pendiente | HTML generado y descargable |
| R-10 | Etiquetado de informes (tags) | Campo tags en formulario y BD | ⏳ pendiente | Etiquetas se guardan y filtran |
| R-11 | Campos de autor, fecha, severidad | Metadatos en formulario y BD | ⏳ pendiente | Metadatos visibles en detalle y filtros |
| R-12 | Soporte para adjuntar archivos (imágenes, capturas) | Campo de upload en formulario | ⏳ pendiente | Archivos almacenados y referenciados |
| R-13 | Interfaz accesible (WCAG 2.1 AA) | Todos los componentes | ⏳ pendiente | Navegación con teclado, ARIA labels |
| R-14 | Diseño responsive (móvil, tablet, desktop) | Tailwind breakpoints | ⏳ pendiente | Layout se adapta a diferentes tamaños |
| R-15 | Metadata SEO básico (title, description) | `app/layout.tsx` | ⏳ pendiente | Tags meta presentes en HTML |
| R-16 | Rate limiting en API endpoints (opcional) | Middleware en API Routes | ⏳ pendiente | Límites de petición aplicados |
| R-17 | Autenticación básica para acciones de escritura (opcional) | NextAuth.js | ⏳ pendiente | Solo usuarios autenticados pueden crear/editar/eliminar |
| R-18 | Backup automático de informes (opcional) | Script cron o Vercel Cron Jobs | ⏳ pendiente | Copias de seguridad realizadas periódicamente |

---
## 🏗️ Marcos Conceptuales

### Modelo de Metadatos
Cada informe tiene los siguientes metadatos:
- **title**: Título descriptivo del informe.
- **slug**: Versión amigable para URL (generada automáticamente del título).
- **author**: Nombre del auditor o equipo.
- **date**: Fecha de realización o publicación del informe.
- **tags**: Array de etiquetas (ej: ["wifi", "handshake", "pmkid"]).
- **severity**: Nivel de severidad (informativo, bajo, medio, alto, crítico).
- **file_path**: Ruta relativa al archivo markdown en el sistema de almacenamiento.

### Almacenamiento de Archivos
- Los archivos markdown se almacenan en el sistema de archivos (local o S3) bajo una estructura de carpetas por año/mes para facilitar la organización.
- Los archivos adjuntos (imágenes, capturas, etc.) se almacenan en una subcarpeta `assets/` junto al markdown o en un bucket separado.

### Renderizado de Markdown
- Se utiliza una biblioteca segura como `marked` o `remark` para convertir markdown a HTML, con sanitization para evitar XSS.
- Se permiten extensiones comunes como tablas, listas de tareas, y bloques de código.

### Exportación a PDF
- Se utiliza `wkhtmltopdf` o `puppeteer` para convertir el HTML renderizado a PDF, respetando estilos básicos y salto de página.

---
## ✅ Justificación de Decisiones Técnicas

| Decisión | Opción elegida | Alternativas evaluadas | Razón |
|----------|----------------|------------------------|-------|
| Frontend | Next.js 14 (App Router) | Create React App, Vite, Nuxt | SSR/SSG para SEO, rutas basadas en sistema de archivos, API routes integradas |
| Lenguaje | TypeScript | JavaScript | Tipado estático para reducir errores en proyectos medianos/grandes |
| Styling | Tailwind CSS v4 | Bootstrap, Material-UI, CSS Modules | Utility-first, personalización fácil, tamaño reducido en producción |
| Base de datos | SQLite / PostgreSQL | MongoDB, Firebase, Airtable | SQLite para desarrollo sencillo, PostgreSQL para producción y escalabilidad |
| Almacenamiento de archivos | Sistema de archivos local / S3 | Base64 en DB, GridFS | Separación de preocupaciones, escalabilidad, costos efectivos |
| API | API Routes de Next.js | Express.js, NestJS, Firebase Functions | Simplicidad de despliegue en Vercel, menor overhead |
| Autenticación | NextAuth.js (opcional) | Auth0, Firebase Auth, sesiones custom | Integración nativa con Next.js, proveedores múltiples |
| Exportación | Pandoc + wkhtmltopdf | LaTeX, browser print to PDF | Pandoc maneja markdown complejo, wkhtmltopdf es sencillo de usar |
| Hosting | Vercel | Netlify, AWS Amplify, Docker | Integración perfecta con Next.js, despliegues serverless, CDN global |

---
## 📦 Estado de Implementación

### Fase 1: MVP (Producto Mínimo Viable)
- [ ] Estructura básica de Next.js con TypeScript y Tailwind CSS.
- [ ] Modelo de datos y migraciones iniciales (si se usa PostgreSQL).
- [ ] API routes para CRUD de informes (sin autenticación inicialmente).
- [ ] Interfaz de listado y detalle básica.
- [ ] Almacenamiento de archivos markdown en sistema de archivos local.
- [ ] Exportación a PDF y HTML (sin estilo avanzado).
- [ ] Búsqueda básica por título y filtros por tags.

### Fase 2: Mejoras de Usabilidad y Características
- [ ] Autenticación opcional con NextAuth.js para proteger rutas de escritura.
- [ ] Edición de formularios existentes.
- [ ] Soporte para adjuntar imágenes y otros recursos.
- [ ] Vista previa de markdown en tiempo real en el formulario.
- [ ] Mejoras en el estilo de exportación PDF (hoja de estilos personalizada).
- [ ] Paginación y búsqueda mejorada (debounce, indicadores de carga).
- [ ] Etiquetado con autocompletado y selección múltiple.

### Fase 3: Características Avanzadas y Optimización
- [ ] Rate limiting y cabeceras de seguridad.
- [ ] Generación automática de índice y tabla de contenidos para documentos largos.
- [ ] Soporte para firmas digitales (hashes SHA-256) y verificación de integridad.
- [ ] Integración opcional con sistemas de gestión de vulnerabilidades (webhooks).
- [ ] Backup automático y versionado de archivos (Git-LFS o similares).
- [ ] Internacionalización (i18n) para interfaz en múltiples idiomas.
- [ ] Modo oscuro/claro basado en preferencia del sistema.

### Backlog inicial (Tareas para iniciar el MVP)
| ID   | Tarea                                                                 | Prioridad | Dependencias |
|------|-----------------------------------------------------------------------|-----------|--------------|
| M1-T1| Inicializar repositorio: `npm init -y`, instalar dependencias básicas (next, react, typescript, tailwindcss) | Alta      | — |
| M1-T2| Configurar TypeScript, ESLint, Prettier y husky para linting en pre-commit | Alta      | M1-T1 |
| M1-T3| Crear estructura de carpetas: `/app`, `/components`, `/lib`, `/public` | Alta      | M1-T1 |
| M1-T4| Diseñar y crear modelo de datos (tipo TypeScript para informe) y función para generar slug | Alta      | M1-T3 |
| M1-T5| Implementar API route POST `/api/reports` para crear un informe (guardar markdown y metadatos) | Alta      | M1-T4 |
| M1-T6| Implementar API route GET `/api/reports` para listar informes con paginación y filtros básicos | Alta      | M1-T5 |
| M1-T7| Crear página de listado `app/page.tsx` con tarjetas de informe y barra de búsqueda simple | Alta      | M1-T6 |
| M1-T8| Crear página de detalle `app/[id]/page.tsx` que lea el markdown y lo renderice | Alta      | M1-T5 |
| M1-T9| Implementar rutas de exportación: GET `/api/reports/:id/export?format=pdf` y `format=html` | Media     | M1-T5 |
| M1-T10| Añadir estilización básica con Tailwind y componentes reutilizables (tarjeta, botón, input) | Media     | M1-T3 |
| M1-T11| Configurar despliegue en Vercel (ajustar `vercel.json` si es necesario) | Media     | M1-T1 |
| M1-T12| Escribir README con instrucciones de instalación y uso | Baja      | M1-T1 |

---
## ⚠️ Limitaciones Conocidas (MVP)

1. **Sin autenticación inicialmente**: Cualquiera puede crear, editar o eliminar expuestos si se despliega sin protección.
2. **Búsqueda básica**: La búsqueda inicial es por coincidencia simple de título y tags, no full-text avanzado.
3. **Exportación PDF limitada**: El estilo del PDF es básico y depende del CSS del HTML generado.
4. **Almacenamiento local en desarrollo**: En producción se recomienda usar S3 o similar para escalabilidad.
5. **Sin versionado de archivos**: Cada sobrescritura reemplaza el archivo markdown; no hay historial de cambios en el sistema de archivos.
6. **Sin procesamiento de imágenes avanzado**: Las imágenes en markdown se muestran tal cual, sin optimización o redimensionamiento.
7. **Sin CI/CD automático**: Las pruebas y despliegues deben configurarse manualmente inicialmente.

---
## 🔐 Seguridad (Consideraciones para MVP)

- **Entrada de usuario**: Sanitizar todo el markdown de entrada para prevenir XSS (usar bibliotecas como `dompurify` o la sanitization de `marked`).
- **Subida de archivos**: Limitar tipos de archivo y tamaño si se implementa upload de adjuntos.
- **Rutas de archivo**: Evitar path traversal al servir archivos markdown o adjuntos (usar bibliotecas como `path` de Node.js y validar).
- **Headers de seguridad**: Implementar headers como `X-Content-Type-Options`, `X-Frame-Options`, `Referrer-Policy` en middleware.
- **Rate limiting**: Implementar límites en las API para prevenir abuso (especialmente en endpoints de exportación).

---
## 📚 Referencias

- Next.js 14 App Router: https://nextjs.org/docs/app
- TypeScript: https://www.typescriptlang.org/
- Tailwind CSS v4: https://tailwindcss.com/
- SQLite: https://www.sqlite.org/
- PostgreSQL: https://www.postgresql.org/
- marked (markdown parser): https://github.com/markedjs/marked
- DOMPurify (sanitization): https://github.com/cure53/DOMPurify
- wkhtmltopdf: https://wkhtmltopdf.org/
- Puppeteer: https://pptr.dev/
- Vercel Deployment: https://vercel.com/docs
- NextAuth.js: https://next-auth.js.org/

---
*Generado por SophIA — Sebastian Velasco's autonomous operating system*