# Diagnostico del Sistema Hermes Agent - 2026-09-05

## Tabla Resumen

| Paso | Descripción | Estado |
|------|-------------|--------|
| 1 | Perfiles | ✅ |
| 2 | Skills instaladas | ❌ |
| 3 | MCPs del dev profile | ✅ |
| 4 | GODMODE jailbreak | ✅ |
| 5 | Dashboard web | ❌ |
| 6 | Configuracion del dashboard vs config real | ❌ |
| 7 | Estado de proyectos | ❌ |
| 8 | Trading bot | ❌ |
| 9 | Cron jobs activos | ✅ |
| 10 | Memory y AGENTS.md | ✅ |
| 11 | Trading — indices sinteticos | ❌ |
| 12 | Gateway | ✅ |

## Paso 1: Perfiles
Todos los perfiles tienen config.yaml con model y provider.

## Paso 2: Skills instaladas
Hub: 23 habilidades, Local: 68 habilidades. Habilidades críticas faltantes: lifecycle spec-creation spec-driven-development subagent-driven-development code-review-and-quality architecture-patterns karpathy-guidelines electron-desktop-dev security-and-hardening nexoaccmanager-ipc-account-specific-pattern

## Paso 3: MCPs del dev profile
Todos los MCPs requeridos están activos.

## Paso 4: GODMODE jailbreak
Jailbreak activo: prefill.json existe con 2 mensajes y config.yaml referencia el archivo.

## Paso 5: Dashboard web
Dashboard no responde en http://127.0.0.1:9119

## Paso 6: Configuracion del dashboard vs config real
Desconexión entre config.yaml y lo que muestra el dashboard.

## Paso 7: Estado de proyectos
Proyectos sin PROJECT.md (legacy): hexstrike-ai wsl2-kernel wsl2-kernel-clean

## Paso 8: Trading bot
Proyecto synthetic-trader no encontrado o falta PROJECT.md.

## Paso 9: Cron jobs activos
Cron jobs activos:
  fee38b880c1d [active]
    Name:      orchestrator-ecosystem-sweep
    Schedule:  0 */2 * * *
    Repeat:    ∞
    Next run:  2026-09-05T16:00:00-05:00
    Deliver:   origin
    Skills:    kanban-orchestrator, hermes-profile-diagnostics
    Workdir:   /home/sebas/.hermes/profiles/gatekeeper
    Last run:  2026-09-05T15:21:02.006733-05:00  ok
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-04T18:00:00-05:00, ran 2026-09-05T15:17:49.969674-05:00 (21h 17m late)
    Execution: completed  eaf134c619f947b0bf67ce72e03fc21c

  f9723501af69 [active]
    Name:      Diagnostico diario SophIA
    Schedule:  0 9 * * *
    Repeat:    ∞
    Next run:  2026-09-06T09:00:00-05:00
    Deliver:   local
    Workdir:   /home/sebas
    Last run:  2026-09-04T14:03:31.008164-05:00  error: RuntimeError: ⚠️ No reply: the turn was stopped because the state database reported structural corruption (the transcript would have been lost on restart). Freeing disk space will not help. Recovery options:
1. Run `hermes doctor --fix`
2. Stop the gateway, then recover with:
   hermes sessions recover --source /home/sebas/.hermes/state.db --inspect-only
   (if it reports recoverable) hermes sessions recover --source /home/sebas/.hermes/state.db --output recovered-state.db
   — recovery snapshots the damaged file first; do NOT run `sqlite3 ... ".recover"` against the live state.db, a vulnerable sqlite3 CLI can corrupt it further
3. Restore from a backup in ~/.hermes/backups/
Then send your message again.  (5 failures in a row)
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-05T09:00:00-05:00, ran 2026-09-05T15:17:49.969674-05:00 (6h 17m late)
    Execution: running  dafcf21645ad4b76beb262041839174f

  8568fa106426 [active]
    Name:      pradx-paper-trading-launch
    Schedule:  0 9 * * *
    Repeat:    ∞
    Next run:  2026-09-06T09:00:00-05:00
    Deliver:   local
    Workdir:   /home/sebas/proyectos/synthetic-trader
    Last run:  2026-09-05T15:17:53.484093-05:00  error: RuntimeError: [drift_skip:silent] Skipped to prevent unintended spend: global inference config drifted since this job was created (model 'z-ai/glm-5.2' -> 'nvidia/nemotron-3-super-120b-a12b'), and this job is unpinned. No inference call was made. To run on the new config, on the host running Hermes pin it explicitly: `hermes cron edit 8568fa106426 --provider <provider> --model <model>` (or pin the original values to keep them). This alert is sent once; the job stays skipped until the config is pinned or restored. See #44585.  (15 failures in a row)
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-05T09:00:00-05:00, ran 2026-09-05T15:17:49.969674-05:00 (6h 17m late)
    Execution: failed  770656b4b5c64373a39cd8bf4fc1ebf1

  04d092054e27 [active]
    Name:      pradx-data-collector
    Schedule:  0 6 * * *
    Repeat:    ∞
    Next run:  2026-09-06T06:00:00-05:00
    Deliver:   local
    Workdir:   /home/sebas/proyectos/synthetic-trader
    Last run:  2026-09-05T15:17:54.316375-05:00  error: RuntimeError: [drift_skip:silent] Skipped to prevent unintended spend: global inference config drifted since this job was created (model 'z-ai/glm-5.2' -> 'nvidia/nemotron-3-super-120b-a12b'), and this job is unpinned. No inference call was made. To run on the new config, on the host running Hermes pin it explicitly: `hermes cron edit 04d092054e27 --provider <provider> --model <model>` (or pin the original values to keep them). This alert is sent once; the job stays skipped until the config is pinned or restored. See #44585.  (17 failures in a row)
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-05T06:00:00-05:00, ran 2026-09-05T15:17:49.969674-05:00 (9h 17m late)
    Execution: failed  02ab43a0b4f744308ab5efae4ec453d5

  17525992f794 [active]
    Name:      cross-project-health-check
    Schedule:  every 30m
    Repeat:    ∞
    Next run:  2026-09-05T15:51:58.080398-05:00
    Deliver:   origin
    Skills:    deep-reasoning, context-engineering, verification-before-completion
    Last run:  2026-09-05T15:21:58.080398-05:00  ok
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-04T17:58:17.301066-05:00, ran 2026-09-05T15:17:49.969674-05:00 (21h 19m late)
    Execution: completed  c8a1bdfd5e244e629ea067472ad3750e

  9184dd0e96d3 [active]
    Name:      mcps-health-check
    Schedule:  every 15m
    Repeat:    ∞
    Next run:  2026-09-05T15:35:57.624131-05:00
    Deliver:   origin
    Skills:    deep-reasoning, context-engineering
    Last run:  2026-09-05T15:20:57.624131-05:00  ok
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-04T17:36:36.892104-05:00, ran 2026-09-05T15:17:49.969674-05:00 (21h 41m late)
    Execution: completed  aaf0d33143cc4ee8a5864cb8f0e945cf

  e7eebfd5a181 [active]
    Name:      memory-consolidation
    Schedule:  every 1440m
    Repeat:    ∞
    Next run:  2026-09-06T15:21:30.582286-05:00
    Deliver:   origin
    Skills:    memory-discipline-enforcement, reasoning-preload
    Last run:  2026-09-05T15:21:30.582286-05:00  ok
    Dispatch:  ⚠ late: scheduled 2026-09-05T14:04:12.584396-05:00, ran 2026-09-05T15:17:49.969674-05:00 (1h 13m late)
    Execution: completed  0ef76781e0c540379030832d65b70d6d

  cdef7f9782e4 [active]
    Name:      skill-inventory
    Schedule:  every 60m
    Repeat:    ∞
    Next run:  2026-09-05T16:17:59.183139-05:00
    Deliver:   origin
    Skills:    skill_view, skills_list
    Last run:  2026-09-05T15:17:59.183139-05:00  error: RuntimeError: [drift_skip:silent] Skipped to prevent unintended spend: global inference config drifted since this job was created (model 'nvidia/nemotron-3.5-lightning-30b-a3b' -> 'nvidia/nemotron-3-super-120b-a12b'), and this job is unpinned. No inference call was made. To run on the new config, on the host running Hermes pin it explicitly: `hermes cron edit cdef7f9782e4 --provider <provider> --model <model>` (or pin the original values to keep them). This alert is sent once; the job stays skipped until the config is pinned or restored. See #44585.  (68 failures in a row)
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-04T18:04:21.108653-05:00, ran 2026-09-05T15:17:49.969674-05:00 (21h 13m late)
    Execution: failed  2224c9d6ceab4badb1d177e25aead570

  96486f2ba9e3 [active]
    Name:      profile-dispatch-test
    Schedule:  every 60m
    Repeat:    ∞
    Next run:  2026-09-05T16:18:01.894787-05:00
    Deliver:   origin
    Skills:    dispatching-parallel-agents, profile-execution-protocol
    Last run:  2026-09-05T15:18:01.894787-05:00  error: RuntimeError: [drift_skip:silent] Skipped to prevent unintended spend: global inference config drifted since this job was created (model 'nvidia/nemotron-3.5-lightning-30b-a3b' -> 'nvidia/nemotron-3-super-120b-a12b'), and this job is unpinned. No inference call was made. To run on the new config, on the host running Hermes pin it explicitly: `hermes cron edit 96486f2ba9e3 --provider <provider> --model <model>` (or pin the original values to keep them). This alert is sent once; the job stays skipped until the config is pinned or restored. See #44585.  (69 failures in a row)
    Dispatch:  ⚠ catch-up after missed fire: scheduled 2026-09-04T18:04:20.969137-05:00, ran 2026-09-05T15:17:49.969674-05:00 (21h 13m late)
    Execution: failed  184698effd6346cd9c14453df1d205da

  f66d14cfe475 [active]
    Name:      mcp-security-weekly-scan
    Schedule:  0 3 * * 0
    Repeat:    ∞
    Next run:  2026-09-06T03:00:00-05:00
    Deliver:   origin
    Skills:    security-and-hardening, enterprise-dev-workflow
    Last run:  2026-08-30T03:00:53.606510-05:00  error: RuntimeError: [drift_skip] Skipped to prevent unintended spend: global inference config drifted since this job was created (model 'nvidia/nemotron-3.5-lightning-30b-a3b' -> 'nvidia/nemotron-3-super-120b-a12b'), and this job is unpinned. No inference call was made. To run on the new config, on the host running Hermes pin it explicitly: `hermes cron edit f66d14cfe475 --provider <provider> --model <model>` (or pin the original values to keep them). This alert is sent once; the job stays skipped until the config is pinned or restored. See #44585.
    Execution: failed  bd75fcf81e30451f8651069bb5679e5f

El job de diagnóstico diario está registrado.

## Paso 10: Memory y AGENTS.md
Memory tiene 10 entradas. Todos los AGENTS.md existen y no están vacíos.

## Paso 11: Trading — indices sinteticos
Perfil trader no tiene config.yaml.

## Paso 12: Gateway
Gateway está running. Uptime: N/A, Memoria: N/A

## Conclusión
SISTEMA: REQUIERE ATENCIÓN
Issues detectados:
- Habilidades críticas faltantes
- Dashboard no responde
- Desconexión entre config.yaml y dashboard
- Proyectos sin PROJECT.md
- Proyecto synthetic-trader no encontrado
- Perfil trader sin config.yaml