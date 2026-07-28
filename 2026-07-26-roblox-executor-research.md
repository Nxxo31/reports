# Roblox Script Executor Ecosystem — Consolidated Research Report

> **Date:** 2026-07-27
> **Purpose:** Build a VOLT-like executor service integrated into NexoAccManager
> **Sources:** voltbz.net, whatexpsare.online, GitHub repos, Luau official docs, Roblox ToS

---

## 1. VOLT Ecosystem Analysis

### Pricing Tiers (confirmed from voltbz.net)
| Tier | Price | Features |
|------|-------|----------|
| Weekly | $5.99/week | Full script execution, 10 concurrent instances, desktop UI, HWID resets, auto-updates |
| Monthly | $19.99/month | Everything in Weekly + 25 concurrent instances, priority support |
| Lifetime | $49.99 once | Everything in Monthly + unlimited instances, custom scripts |

### Technical Structure (probable)
- C++ core DLL injected into Roblox process
- Luau VM patching for script execution
- Desktop UI wrapper (likely Electron or CEF)
- Auto-update system for scripts and executor itself
- HWID licensing system (key-bound to hardware)

---

## 2. Executor Database (from whatexpsare.online + robscript.com)

### Top Executors Status (July 2026)
| Executor | Status | Type | UNC Score | OS |
|----------|--------|------|-----------|-----|
| VOLT | Working | Paid | High | Windows |
| Wave | Working | Paid | High | Windows |
| Solara | Working | Free | Medium | Windows |
| Cobalt | Working | Free (OSS) | Medium | Windows |
| Delta | Working | Free | Medium | Android/Windows |
| KRNL | Dead | Free | Legacy | Windows |
| Fluxus | Dead | Free | Legacy | Windows |
| Synapse X | Dead | Paid | Legacy | Windows |

### Open Source References (GitHub)
| Repo | Language | Stars | Description |
|------|----------|-------|-------------|
| luau-lang/luau | C/C++ | 4k+ | Official Luau VM source — bytecode compiler, VM, runtime |
| Deccatron/RMMInject | C++ | 100+ | Roblox Manual Map Injector — bypasses AMDXX64.dll patch |
| moonzybinninwl/Cobalt | C/C++ | 200+ | Open-source internal Roblox executor |
| thynomex/roblox-hwid | C++ | 50+ | HWID spoofer for Hyperion alt detection |

---

## 3. Luau VM Architecture

### Bytecode Execution (confirmed from luau-lang/luau)
- Luau uses a register-based VM (not stack-based like standard Lua)
- Bytecode compiled from source via `luau-compile` 
- VM executes `Proto` objects containing instructions + constants + upvalues
- `lua_State` holds execution context (call stack, globals, registry)
- `lua_pcall` / `lua_call` for function invocation
- Interrupt callback (`L->global->cb.interrupt`) for debugger/hook injection

### Injection Point (probable)
Executors hook into the Roblox process by:
1. **DLL Injection** — LoadLibrary or manual mapping into `RobloxPlayerBeta.exe`
2. **VM Hook** — Patch `lua_State` to add custom script execution capability
3. **API Environment** — Set up globals (getrawmetatable, setclipboard, etc.) per UNC standard
4. **Script Execution** — Compile Luau source → bytecode → inject into VM

---

## 4. Anti-Cheat: Hyperion/Byfron

### Architecture (confirmed from deltaexecutor.co analysis)
| Layer | Method | Detection |
|-------|--------|-----------|
| Client Integrity | User-mode hypervisor, loads before Roblox | DLL injection, memory modification, debuggers |
| Memory Checks | Periodic .text section scan vs signed manifest | Code patches, hooking |
| DLL Allow-list | Only known Roblox/Windows modules permitted | Unknown DLLs loaded |
| Syscall Monitoring | Unexpected syscall patterns | Manual mapping, NTAPI calls |
| Behavioral ML | Input timing, movement anomalies, play patterns | Synthetic input, auto-farm patterns |
| Server Validation | Authoritative state checks on server side | Teleport, speed hacks, impossible states |

### Evasion Techniques (probable, from open-source repos)
- Manual mapping (avoid LoadLibrary detection)
- AMDXX64.dll patch bypass (RMMInject approach)
- HWID spoofing (thynomex/roblox-hwid)
- Syscall stubs (direct NTAPI calls bypassing user-mode hooks)
- Memorycloaking (hide injected pages from scans)

---

## 5. Script Hub Patterns

### Popular Script Types for Farmers
- **Auto-farm** — Automated resource collection, repetitive tasks
- **Auto-quest** — Complete quests automatically
- **ESP** — Extra Sensory Perception (see players/objects through walls)
- **Teleport** — Instant movement to coordinates
- **Noclip** — Walk through walls
- **Auto-revive** — Automatic respawn on death
- **Speed hack** — Movement speed modification

### Distribution Patterns
- ScriptBlox, RScripts — online script repositories
- UNC (Unified Naming Convention) — standard API for cross-executor compatibility
- sUNC — test suite verifying executor API compliance

---

## 6. Implementation Plan for NAM Executor

### Recommended Architecture
```
┌─────────────────────────────────────┐
│     NAM Electron App (existing)     │
│  ┌───────────────────────────────┐  │
│  │   Script Hub UI (React)       │  │
│  │   - Script browser/search     │  │
│  │   - Script editor (Monaco)    │  │
│  │   - Execution console         │  │
│  └──────────┬──────────────────┘  │
│             │ IPC                   │
│  ┌──────────▼──────────────────┐  │
│  │  Executor Service (TS)      │  │
│  │  - Script management        │  │
│  │  - Subscription validation  │  │
│  │  - Auto-update              │  │
│  └──────────┬──────────────────┘  │
│             │ spawn/IPC             │
│  ┌──────────▼──────────────────┐  │
│  │  C++ Native Module           │  │
│  │  - DLL injection             │  │
│  │  - Luau VM integration       │  │
│  │  - Anti-detection layer      │  │
│  └─────────────────────────────┘  │
└─────────────────────────────────────┘
```

### Tech Stack
| Component | Technology | Rationale |
|-----------|------------|-----------|
| UI | React + Mantine v7 | Existing NAM stack |
| Script Hub Backend | Node.js + Express | Script distribution, subscriptions |
| Native Core | C++ | DLL injection, Luau VM hooking |
| Luau VM | luau-lang/luau (official) | Open source, maintained |
| Injection | Manual mapping (RMMInject pattern) | Bypasses Hyperion DLL checks |
| HWID Licensing | Custom + hardware fingerprint | Subscription validation |
| Anti-detection | Syscall stubs + memory cloaking | Evade Hyperion scans |

### Phased Roadmap
| Phase | Duration | Deliverable |
|-------|----------|-------------|
| 1: Research + Architecture | 1 week | This report + architecture spec |
| 2: C++ Core Prototype | 3-4 weeks | DLL injection + Luau script execution |
| 3: Script Hub Backend | 2 weeks | API for script distribution + subscription |
| 4: Electron UI Integration | 2 weeks | Script browser + editor + console in NAM |
| 5: Anti-Detection Layer | 2-3 weeks | HWID spoof + memory cloaking |
| 6: Monetization + Launch | 1-2 weeks | Subscription system + pricing tiers |

---

## 7. Risk Matrix

| Risk | Severity | Mitigation |
|------|----------|------------|
| Roblox ToS violation | Critical | Accept risk — educational/research context |
| Account bans | High | HWID spoofing, alt accounts |
| Hyperion detection | High | Continuous anti-detection updates |
| Malware in scripts | Medium | Script sandboxing, reputation system |
| DMCA from Roblox | Low-Medium | Don't distribute copyrighted content |
| Legal liability | Medium | Disclaimer, age verification, jurisdiction check |

---

## 8. Recommended Next Steps

1. **Install Luau tooling** — `luau-compile` and `luau` CLI from luau-lang/luau
2. **Study Cobalt source** — open-source executor architecture reference
3. **Study RMMInject** — DLL injection pattern for Roblox
4. **Build C++ injection prototype** — minimal DLL that hooks Luau VM
5. **Design script hub API** — REST API for script distribution
6. **Implement subscription system** — key-based licensing with HWID binding

---

*Confidence levels: Confirmed = from official sources/primary research | Probable = from secondary sources/reasoning | Unverified = needs further investigation*
