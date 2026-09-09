# Diagnóstico Hermes Agent - 2026-09-06

## Resumen Ejecutivo

## Paso 1: Perfiles

Checking default:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b
Checking dev:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b
Checking orchestrator:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b
Checking research:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b
Checking designer:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b
Checking trader:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b
Checking ciberseguridad:
  [OK] config.yaml existe
  [WARN] Modelo o proveedor no encontrado en credential_pool_strategies
      credential_pool_strategies:
        nvidia: round_robin
        provider: nvidia
        default: nvidia/nemotron-3.5-lightning-30b-a3b

## Paso 2: Skills instaladas

Skills hub: 23
Skills local: 91
Total instaladas: 114

  [OK] lifecycle
  [FAIL] spec-driven-development
  [FAIL] subagent-driven-development
  [FAIL] code-review-and-quality
  [FAIL] architecture-patterns
  [OK] karpathy-guidelines
  [OK] electron-desktop-dev
  [FAIL] security-and-hardening
  Instalando habilidades faltantes...

## Paso 3: MCPs del dev profile

  [OK] github: enabled
  [OK] context7: enabled
  [OK] playwright: enabled
  [OK] filesystem: enabled
  [OK] zenith: enabled
  [OK] lsp-intelligence: enabled
  [FAIL] mcp-code-review: disabled o no encontrado
  [OK] firecrawl: enabled
  [FAIL] sequential-thinking: disabled o no encontrado

## Paso 4: GODMODE jailbreak

  [WARN] prefill.json tiene menos de 2 mensajes: 0
0
  [FAIL] agent.prefill_messages_file no encontrado en config.default

## Paso 5: Dashboard web

  [FAIL] Dashboard no responde en http://127.0.0.1:9119
  Necesita ser iniciado con: hermes dashboard

## Paso 6: Configuracion del dashboard vs config real

  [FAIL] model.default no es z-ai/glm-5.2
    Valor actual: 
  [FAIL] agent.prefill_messages_file no visible en config

## Paso 7: Estado de proyectos

Proyecto: NexoAccManager
# PROJECT.md — NexoAccManager
>
> **Estado:** Activo | **Versión:** 5.0.0 | **Última actualización:** 2026-08-31 (sesión: Tests unitarios completados para servicios críticos)
> Lint 0 errors, TypeScript 0 errors, build exit 0 (AppImage generado). Fixes críticos de seguridad aplicados (CryptoService, LocalApiService). IPC **totalmente sincronizado (102=102=102)**. Pendiente: Ninguno (todas las tareas pendientes completadas).
>

Proyecto: Tic-Tac-Toe
# Tic-Tac-Toe (Juego del Gato)

> **Estado:** Fases 1-3 completadas | **Versión:** 0.2.0 | **Última actualización:** 2026-08-03

---

Proyecto: contract-guard
# PROJECT.md — Contract Guard

> **Estado:** Activo | **Versión:** 1.0.0 | **Última actualización:** 2026-07-31

---

Proyecto: e14-fraud-detector
# PROJECT.md — E-14 Fraud Detector

> **Estado:** Activo | **Versión:** 0.3.0 | **Última actualización:** 2026-07-31

---

Proyecto: flag-edge
# PROJECT.md — Flag Edge

> **Estado:** RELEASED ✅ | **Versión:** 1.0 (tag v1.0) | **Última actualización:** 2026-08-04

---

Proyecto: grani-usco
# PROJECT.md — Grani USCO

> **Estado:** Activo | **Versión:** 0.1.0 | **Última actualización:** 2026-07-31

---

Proyecto: nexocore
# NexoCore — Plataforma SaaS Multi-Tenant ERP/CRM/Analytics para PYMEs
**Versión:** 2026.08.09 | **Sprint:** 1 (Semana 1) — Base y plantillas
**Estado:** EN PROGRESO · Sprint 1 completo, Sprints 2-4 planificados

## Resumen

Proyecto: nva-demons
# PROJECT.md — NVA Demons

> **Estado:** Activo | **Versión:** 0.2.0 (Fase 2 FXs) | **Última actualización:** 2026-08-03

---

Proyecto: nx-music
# PROJECT.md — NX Music

> **Estado:** Fase 2/6 completada (Quality Verifier + Tagger) — Fase 2 de 6
> **Limitación:** El frontend Flutter no está iniciado — 0% completado. Solo motor backend en Fase 2
> **Última actualización:** 2026-08-09

Proyecto: portafolio
# PROJECT.md — Portafolio (Constellation)
>
> **Estado:** Activo | **Versión:** 0.1.1 + S-02 Dark Mode Toggle (finalizado) + Deploy-ready Vercel | **Stack:** Next.js 16 + Tailwind v4 + Three.js
>
> ## 📋 Visión General

Proyecto: prompt-engineering-pipeline
# PROJECT.md — prompt-engineering-pipeline

> **Estado:** Stub autogenerado por ecosystem-sweep cron (2026-08-21).
> Pendiente de completar por el owner.


Proyecto: quantum-vqe-service
# Quantum VQE Algorithm Validation Service

## Overview
A REST API service for quantum computing resource access and VQE (Variational Quantum Eigensolver) simulations for battery material research. Provides API access to IBM Quantum and AWS Braket backends with result validation using classical HPC.


Proyecto: reports
# PROJECT.md — Reports (Gestión de Informes de Investigación)

> **Estado:** Activo | **Versión:** MVP (Fase 1) | **Última actualización:** 2026-07-31

---

Proyecto: supply-radar
# PROJECT.md — Supply Radar

> **Estado:** Activo | **Versión:** 1.2.0 | **Última actualización:** 2026-08-03

---

Proyecto: synthetic-trader
# PROJECT.md — Synthetic Trader SaaS

> **Estado:** Activo — Paper Trading 24/7 en curso (stpRNG + step_index) | **Versión:** 0.4.0 | **Última actualización:** 2026-08-14

---

Proyecto: triqui
# PROJECT.md — Triqui (Tic-Tac-Toe)

> **Estado:** Activo | **Versión:** 2.0.0 | **Última actualización:** 2026-07-31
> **Repositorio:** https://github.com/Nxxo31/triqui
> **Deploy:** https://triqui-coral.vercel.app

Proyecto: wifi-pentest
# wifi-pentest

## Estado
Activo — herramientas de pentesting WiFi.


Proyecto: wordlists
# wordlists

## Estado
Activo — wordlists para auditorías de seguridad.


  [INFO] hexstrike-ai no encontrado en /home/sebas/proyectos
  [INFO] wsl2-kernel no encontrado en /home/sebas/proyectos
  [INFO] wsl2-kernel-clean no encontrado en /home/sebas/proyectos

## Paso 8: Trading bot

  Proyecto synthetic-trader encontrado
  Estado: > **Estado:** Activo — Paper Trading 24/7 en curso (stpRNG + step_index) | **Versión:** 0.4.0 | **Última actualización:** 2026-08-14
  Cron jobs del trader:
    Ninguno encontrado

## Paso 9: Cron jobs activos

Cron jobs activos:
/tmp/diagnose_fixed.sh: line 189: cronjob: command not found

## Paso 10: Memory y AGENTS.md

  [FAIL] Memory tool no responde
  [OK] AGENTS.md (191 líneas)
  [OK] AGENTS.md (94 líneas)
  [OK] AGENTS.md (86 líneas)
  [OK] AGENTS.md (69 líneas)
  [OK] AGENTS.md (126 líneas)
  [OK] AGENTS.md (34 líneas)
  [OK] AGENTS.md (47 líneas)

## Paso 11: Trading — indices sinteticos

  Perfil trader configurado
    [FAIL] ccxt: disabled o no encontrado
    [OK] deriv: enabled
    [FAIL] yfinance: disabled o no encontrado

## Paso 12: Gateway

  [OK] Gateway está running
    Uptime: 
    Memoria: 2.5G

## Paso 13: Generar reporte

  Reporte generado en: /home/sebas/proyectos/reports/diagnostico-2026-09-06.md
SISTEMA: REQUIERE ATENCION
Issues encontrados:
  - Se encontraron fallos

## Paso 14: Memory update

  Falló actualización de memory para: Se encontraron fallos
  Issues guardados en memory

--- Fin del reporte ---
