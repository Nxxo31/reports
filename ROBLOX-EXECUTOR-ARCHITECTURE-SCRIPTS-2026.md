# Roblox Executor Architecture & Script Construction Research (2026)

## Purpose
Technical architecture research for building a Roblox script executor from scratch (Voltbz-style), based on 2026 primary sources: open-source executor codebases (Polycheat, TaaprWareV2, XenoExecutor), Luau Lang official docs, Unified Naming Convention (UNC) API standard, and script hub library patterns (Rayfield, Fluent).

> ⚠️ Educational/Research Document. Covers publicly documented architectural patterns for understanding how these systems are constructed.

---

## 1. High-Level Architecture Pattern

All modern Roblox executors share the same multi-layer pattern (verified across Polycheat, TaaprWareV2, XenoExecutor sources):

```
┌─────────────────────────────────────────────────────────────┐
│                    EXECUTOR APPLICATION                       │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐  │
│  │  UI Layer   │  │  Injector   │  │  Executor Core       │  │
│  │  (C#/ImGui) │→ │  Module     │  │  (C++ DLL)           │  │
│  │             │  │             │  │  ┌────────────────┐  │  │
│  │ - Script    │  │ - Process   │  │  │  Luau VM       │  │  │
│  │   editor    │  │   handle    │  │  │  (embedded)    │  │  │
│  │ - Script    │  │ - Alloc     │  │  ├────────────────┤  │  │
│  │   hub       │  │   memory    │  │  │  Custom API    │  │  │
│  │ - Execute   │  │ - Write     │  │  │  bindings       │  │  │
│  │   button    │  │   DLL path │  │  ├────────────────┤  │  │
│  │ - Key       │  │ - Create   │  │  │  Anti-detect    │  │  │
│  │   system    │  │   thread    │  │  │  layer         │  │  │
│  │             │  │             │  │  ├────────────────┤  │  │
│  │             │  │             │  │  │  Scheduler     │  │  │
│  │             │  │             │  │  │  integration   │  │  │
│  └─────────────┘  └─────────────┘  │  └────────────────┘  │  │
│                                     └─────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                         ↓ (injects DLL)
                ┌──────────────────┐
                │   Roblox Process  │
                │  (RobloxPlayer.exe)│
                │                    │
                │  Game's Luau VM     │
                │  (executor hooks)   │
                └──────────────────┘
```

### Component Responsibilities

1. **UI Layer (C#/ImGui/Electron)**: Script editor (code editor with syntax highlighting), script hub browser, execute button, key system, settings
2. **Injector**: Locates Roblox process, allocates memory, writes DLL path, creates remote thread calling `LoadLibraryA` to load executor DLL
3. **Executor Core (DLL)**: Runs inside Roblox process — contains Luau VM (or hooks existing game VM), exposes custom API functions, integrates with game scheduler, includes anti-detection layer
4. **Anti-Detection**: Obfuscation, syscall spoofing, memory hiding, signature randomization

---

## 2. Injector Module — Technical Patterns

### Pattern A: CreateRemoteThread + LoadLibraryA (Classic)
From WeAreDevs tutorials and open-source executors:

```cpp
// 1. Find Roblox process
HWND window = FindWindowA(nullptr, "Roblox");
DWORD procId;
GetWindowThreadProcessId(window, &procId);

// 2. Open process with required permissions
HANDLE hProcess = OpenProcess(
    PROCESS_VM_OPERATION | PROCESS_VM_WRITE | PROCESS_CREATE_THREAD,
    FALSE, procId
);

// 3. Allocate memory for DLL path in target process
const char* dllPath = "C:\\executor.dll";
SIZE_T pathLen = strlen(dllPath) + 1;
LPVOID dllPathMem = VirtualAllocEx(hProcess, nullptr, pathLen,
    MEM_COMMIT, PAGE_EXECUTE_READWRITE);

// 4. Write DLL path string into allocated memory
WriteProcessMemory(hProcess, dllPathMem, dllPath, pathLen, nullptr);

// 5. Get LoadLibraryA address (same across processes on same Windows)
LPVOID loadLibraryAddr = GetProcAddress(
    GetModuleHandle("kernel32.dll"), "LoadLibraryA"
);

// 6. Create remote thread that calls LoadLibraryA(dllPath)
HANDLE hThread = CreateRemoteThread(hProcess, nullptr, 0,
    (LPTHREAD_START_ROUTINE)loadLibraryAddr, dllPathMem, 0, nullptr);

// 7. Wait for DLL to load, cleanup
WaitForSingleObject(hThread, INFINITE);
VirtualFreeEx(hProcess, dllPathMem, 0, MEM_RELEASE);
CloseHandle(hThread);
CloseHandle(hProcess);
```

### Pattern B: Manual Mapping (More Stealthy)
Bypasses `LoadLibraryA` entirely:
1. Allocate memory for entire DLL image
2. Manually parse PE headers, map sections to allocated regions
3. Resolve imports by walking IAT and calling `GetProcAddress` for each
4. Apply relocations if base address differs from preferred
5. Call `DllMain` with `DLL_PROCESS_ATTACH` via remote thread
6. No module entry in PEB's loaded module list (harder to detect)

### Pattern C: APC Queuing (APC Injection)
Queue Asynchronous Procedure Calls to existing threads instead of creating new ones — less detectable than CreateRemoteThread:
- Target thread must be in alertable state
- Use `QueueUserAPC` with load function address
- More stealthy, more fragile

### Hyperion Considerations (2026)
Modern Hyperion intercepts all these techniques via:
- **Instrumentation Callback (IC)**: Monitors UM↔KM transitions
- **DllMain Notification Callbacks**: `PsSetLoadImageNotifyRoutine` equivalent in user mode
- **Integrity Checks**: Scans `.text` section periodically against manifest

Advanced executors use:
- **Race Condition Injection**: Inject before Hyperion initializes (narrow timing)
- **Memory Mapping Tricks**: PEB spoofing, section encryption, encrypted+decrypted dual views
- **IC Manipulation**: Hook instrumentation callback to intercept exceptions before Hyperion sees them
- **Kernel-Level Drivers**: Signed drivers with direct object callbacks (highest risk)

---

## 3. Executor Core — Luau VM Integration Patterns

### Option 1: Embedded Luau VM (Polycheat Pattern — VM-Integrated)
From Polycheat README (2026 open-source):
- Native DLL injection into Roblox client
- Direct interaction with game's Luau VM structures
- Native thread scheduling into game runtime
- Native closure/proto access
- Bytecode dumping and execution
- Hooking internal VM functions (`lua_resume`)
- Custom environment bridging

**Key insight from Polycheat author**: "Executing Lua code itself turned out to be relatively easy. The difficult part was making execution behave naturally inside the game's runtime — yielding correctly, resuming safely, surviving rendering frames, interacting with callbacks, and avoiding broken coroutine state."

### Option 2: Bytecode Injection (XenoExecutor Pattern)
From XenoExecutor README (2026):
- Writes unsigned bytecode into Roblox core module script
- Uses `RuntimeScriptService::RunScript` to execute
- More stable than standalone VM
- Detected method (publicly documented)

### Option 3: New Luau State (Standalone)
From TaaprWareV2 source:
- Creates new `lua_State` via `lua_newstate`
- Uses `RuntimeScriptService::RunScript` for execution
- Hooks terrain function to route lua state to C++ code
- Requires offsets: `rbx_getscheduler`, `rbx_addscript`, `rbx_runscript`, `rbx_deserializer_detour`

### Luau Official Embedding API (from luau-lang/luau README)

```cpp
// 1. Create Luau state (requires linking Luau.Compiler + Luau.VM in CMake)
lua_State* L = luaL_newstate();
luaL_openlibs(L);

// 2. Register custom globals (game-API exposure)
static int customFunction(lua_State* L) {
    const char* arg = lua_tostring(L, 1);
    // Do native work...
    lua_pushstring(L, "result");
    return 1;
}

lua_newtable(L);
lua_pushcfunction(L, customFunction, "customFunction");
lua_setfield(L, -2, "customFunction");
lua_setreadonly(L, -1, true);
lua_setglobal(L, "game");

// 3. Compile source to bytecode
size_t bytecodeSize = 0;
char* bytecode = luau_compile(sourceCode, strlen(sourceCode), NULL, &bytecodeSize);

// 4. Load bytecode into VM
int result = luau_load(L, "=user_script", bytecode, bytecodeSize, 0);
free(bytecode);

if (result == 0) {
    // 5. Create thread, sandbox, resume
    lua_State* script = lua_newthread(L);
    lua_pushvalue(L, -2);
    lua_remove(L, -3);
    lua_xmove(L, script, 1);
    lua_sandboxthread(script);
    int status = lua_resume(script, nullptr, 0);
    // handle status (0=success, LUA_YIELD=coroutine)
}
```

**Pattern Insight from Luau Discussion #2412**:
> "You do this from the host side through Luau's C API before you run user code. It is not FFI from inside Luau; the embedding program registers C/C++ functions, tables, or userdata into the Lua global environment."

### Key Luau C API Functions

| Function | Purpose |
|----------|---------|
| `lua_newstate` / `luaL_newstate` | Create VM state |
| `luau_compile` | Compile Luau source to bytecode |
| `luau_load` | Load bytecode into VM |
| `lua_resume` | Resume coroutine script execution |
| `lua_pcall` | Protected call (catches errors) |
| `lua_newthread` | Create new coroutine |
| `lua_sandboxthread` | Sandbox execution environment |
| `lua_pushcfunction` | Register C function callable from Lua |
| `lua_newtable` / `lua_setfield` / `lua_setglobal` | Build global tables |
| `lua_setreadonly` | Lock table read-only |
| `lua_setfenv` / `lua_getfenv` | Set/get function environment |
| `lua_newuserdatadtor` | Allocate userdata with destructor (no `__gc`) |

---

## 4. UNC (Unified Naming Convention) API Standard

The UNC (unified-naming-convention/NamingStandard on GitHub, archived May 2024) defines the standard script-facing API every executor must expose. Script compatibility depends on UNC compliance. Verified by running `UNCCheckEnv.lua` in the executor environment.

### Scripts API (sourced from UNC repo)

| Function | Purpose | Example |
|----------|---------|---------|
| `getgenv()` | Executor's custom global env | `getgenv().__LOADED = true` |
| `getrenv()` | Game client global env | Access `require` slot, hook it |
| `getgc(includeTables)` | List of GC objects | Memory enumeration |
| `getloadedmodules(excludeCore)` | Loaded ModuleScripts list | Iterate scripts |
| `getrunningscripts()` | Currently running scripts | Detect active scripts |
| `getscripts()` | Every script in game | Script enumeration |
| `getsenv(script)` | Script's global env | Access script variables |
| `getscriptbytecode(script)` | Raw Luau bytecode | Decompile/dump |
| `getscriptclosure(script)` | Closure from bytecode | Recreate script behavior |
| `getscripthash(script)` | SHA384 of bytecode | Detect script changes |
| `getthreadidentity()` | Current thread identity (1-8) | Permission escalation |
| `setthreadidentity(level)` | Set thread identity (8=max) | Bypass security restrictions |

### Metatable API (UNC)

| Function | Purpose |
|----------|---------|
| `getrawmetatable(obj)` | Get locked metatable |
| `setrawmetatable(obj, mt)` | Set locked metatable |
| `hookmetamethod(obj, method, hook)` | Replace `__index`/`__namecall` etc |
| `getnamecallmethod()` | Name of method triggering `__namecall` |

### Hooking Example (UNC pattern)
```lua
-- Hook __namecall to block game:service()
local refs = {}
refs.__namecall = hookmetamethod(game, "__namecall", function(...)
    local self = ...
    local method = getnamecallmethod()
    if self == game and method == "service" then
        error("Not allowed to run game:service()")
    end
    return refs.__namecall(...)
end)
```

### Function Hooking (UNC)
```lua
-- hookfunction with original reference
local refs = {}
refs.require = hookfunction(require, function(...)
    local module = ...
    if typeof(module) == "Instance" and module:IsA("ModuleScript") then
        -- Inspect/block module
    end
    return refs.require(...)
end)
```

### Other Key UNC Functions (from E-unc test suite)
- `hookfunction` / `replaceclosure`
- `getrawmetatable` / `setrawmetatable`
- `getgenv` / `getrenv`
- `getcallingscript`
- `getnamecallmethod` / `setnamecallmethod`
- `getfflag` / `setfflag`
- `gethiddenproperty`
- `iscclosure`
- `checkcaller`
- `identifyexecutor`
- `getplatform`

---

## 5. Script Hub UI Patterns (2026)

### Popular UI Libraries
| Library | Load Method | Notable Features |
|---------|------------|------------------|
| **Rayfield** | `loadstring(game:HttpGet('https://sirius.menu/rayfield'))()` | Open-source, Discord integration, key system, notifications, themes |
| **Fluent** | `loadstring(game:HttpGet("...Fluent"))()` | Modern UI, acrylic themes, SaveManager/InterfaceManager, search |
| **Luna** | `loadstring` pattern | Lightweight, Material design |

### Rayfield Usage Pattern (most common)
```lua
local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
    Name = "Script Hub | Version",
    LoadingTitle = "Script Hub",
    LoadingSubtitle = "By Author",
    ConfigurationSaving = {
        Enabled = true,
        FolderName = "MyHub",
        FileName = "Config"
    },
    Discord = {
        Enabled = true,
        Invite = "discordCode",
        RememberJoins = true
    },
    KeySystem = false,
    KeySettings = {
        Title = "Key System",
        Subtitle = "Enter key",
        Note = "Get from Discord",
        FileName = "Key",
        SaveKey = true,
        GrabKeyFromSite = false,
        Key = {"yourkey"}
    }
})

local MainTab = Window:CreateTab("Main", 4483362458)

-- Button
MainTab:CreateButton({
    Name = "Infinite Yield",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
    end
})

-- Toggle
MainTab:CreateToggle({
    Name = "Auto Farm",
    CurrentValue = false,
    Flag = "AutoFarm",
    Callback = function(value)
        getgenv().AutoFarm = value
    end
})

-- Slider
MainTab:CreateSlider({
    Name = "WalkSpeed",
    Range = {16, 500},
    Increment = 1,
    Suffix = "Speed",
    CurrentValue = 16,
    Flag = "WalkSpeed",
    Callback = function(value)
        game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = value
    end
})
```

### Script Hub Architecture (typical executor)
```
script-hub/
├── scripts/
│   ├── arsenal.lua        # Game-specific scripts
│   ├── brookhaven.lua
│   ├── bloxfruits.lua
│   └── general/
│       ├── infiniteyield.lua
│       ├── dex.lua
│       └── esp.lua
├── categories.json        # Categorization metadata
├── config.json            # User preferences
└── update.json            # Cloud sync metadata
```

---

## 6. Common Script Construction Patterns

### Pattern A: ESP/Visual Scripts
```lua
-- Drawing API (executor-provided)
local Drawing = Drawing.new("Text")  -- executor API
Drawing.Text = "Player Name"
Drawing.Color = Color3.fromRGB(255, 0, 0)
Drawing.Size = 14
Drawing.Outline = true
Drawing.Center = true
Drawing.Visible = true

-- Update positions each frame
RunService.RenderStepped:Connect(function()
    for _, player in ipairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character then
            local head = player.Character:FindFirstChild("Head")
            if head then
                local screenPos, onScreen = Camera:WorldToViewportPoint(head.Position)
                if onScreen then
                    Drawing.Position = Vector2.new(screenPos.X, screenPos.Y)
                    Drawing.Text = player.Name
                    Drawing.Visible = true
                else
                    Drawing.Visible = false
                end
            end
        end
    end
end)
```

### Pattern B: Admin/Command Scripts
```lua
-- Hook __namecall to intercept :GetService etc
local oldNamecall
oldNamecall = hookmetamethod(game, "__namecall", function(...)
    local method = getnamecallmethod()
    local args = {...}
    local self = args[1]

    -- Custom command interception
    if method == "FireServer" and typeof(self) == "Instance" then
        -- Modify arguments
    end

    return oldNamecall(...)
end)

-- Direct memory access
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Hook Humanoid for speed/jump
local function applySpeed(newSpeed)
    local humanoid = LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = newSpeed
    end
end

-- Loop on RenderStepped for god mode
RunService.Heartbeat:Connect(function()
    local humanoid = LocalPlayer.Character and LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    if humanoid and getgenv().GodMode then
        humanoid.Health = humanoid.MaxHealth
    end
end)
```

### Pattern C: Auto-Farm Scripts
```lua
-- Character state manipulation
local VirtualUser = game:GetService("VirtualUser")
local VirtualInputManager = game:GetService("VirtualInputManager")

local function farm()
    while getgenv().AutoFarm do
        local char = LocalPlayer.Character
        if not char then task.wait(1); continue end

        local humanoid = char:FindFirstChildOfClass("Humanoid")
        local target = findNearestTarget()
        if target and humanoid then
            -- Move to target
            humanoid:MoveTo(target.Position)
            target.PositionReached:Wait()

            -- Attack via RemoteEvent
            local args = {
                [1] = "Attack",
                [2] = target
            }
            game:GetService("ReplicatedStorage").Events.Attack:FireServer(unpack(args))
        end
        task.wait(0.1)
    end
end

-- Multi-instance launch from NAM/NX Manager context
local function multiLaunchAccount(accountId)
    -- Uses executor's multi-instance feature
    local Roblox = game:GetService("Roblox")  -- executor-specific
    Roblox:LaunchInstance(accountId)
end
```

### Pattern D: Modifying CoreScripts
```lua
-- Get CoreScript and modify source
local RobloxGui = game:GetService("CoreGui"):WaitForChild("RobloxGui")
local resetCallback = RobloxGui:FindFirstChild("ResetCallback")
if resetCallback then
    local env = getsenv(resetCallback)
    -- Inject custom behavior
    env.customFunction = function()
        print("Intercepted reset")
    end
end

-- Compile and run custom bytecode
local customSource = [[
    local Players = game:GetService("Players")
    local LocalPlayer = Players.LocalPlayer
    print("Running as " .. LocalPlayer.Name)
]]

local bytecode = getgenv().compileSource(customSource)  -- executor-specific
loadstring(bytecode)()
```

---

## 7. Build Tooling & Stack Recommendation (for building from scratch)

### Dependencies
- **C++ Compiler**: MSVC 2022 x64 (Roblox is Windows-only native)
- **Build System**: CMake 3.20+
- **Luau Source**: Static link `luau-lang/luau` (official repo, MIT license)
- **UI Framework**: ImGUI (C++) OR C# Avalonia (like TaaprWareV2) OR Electron if standalone
- **Decompiler**: Atrexus/Unluau (for bytecode disassembly)
- **HTTP Library**: httplib, OpenSSL (for cloud script hub)
- **Compression**: zstd, xxhash (for script hub payload)
- **Native Dependencies**: vcpkg for zstd, openssl, httplib

### CMake Skeleton (based on Polycheat/XenoExecutor)
```cmake
cmake_minimum_required(VERSION 3.20)
project(ROREX LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS OFF)

# Add Luau as submodule
add_subdirectory(third_party/luau)

# Executor DLL target
add_library(rorex_executor SHARED
    src/DllMain.cpp
    src/Executor.cpp
    src/LuauVM.cpp
    src/APIBindings.cpp
    src/Scheduler.cpp
    src/AntiDetect.cpp
)
target_link_libraries(rorex_executor PRIVATE
    Luau.Compiler
    Luau.VM
    Luau.Ast
)

# Injector executable target
add_executable(rorex_injector
    src/Injector.cpp
    src/ProcessUtils.cpp
)
target_link_libraries(rorex_injector PRIVATE
    psapi
    ntdll
)
```

### File Structure Pattern
```
rorex/
├── CMakeLists.txt
├── third_party/
│   ├── luau/                    # luau-lang/luau git submodule
│   └── unluau/                  # Atrexus/unluau for decompilation
├── src/
│   ├── DllMain.cpp              # DLL entry point (Injectected into Roblox)
│   ├── Executor.cpp             # Main executor class
│   ├── LuauVM.cpp               # Luau VM integration
│   ├── APIBindings.cpp          # UNC API function exposure
│   ├── Scheduler.cpp            # Thread scheduling into game runtime
│   ├── AntiDetect.cpp           # Evasion layer
│   └── Injector.cpp             # External injector (separate exe)
├── scripts/
│   ├── UNCCheckEnv.lua          # UNC compliance test
│   ├── template_script.lua      # User template
│   └── demo_esp.lua             # Demo ESP script
└── ui/                          # Separate UI project (C#/Electron)
    └── MainForm.cs
```

---

## 8. Feasibility & Effort Estimate

### Components & Complexity (2026 reference baseline)

| Component | Effort | Difficulty | Notes |
|-----------|--------|-----------|-------|
| **UI Layer** | M-H | Medium | ImGui or Electron — well-understood |
| **Injector (CreateRemoteThread)** | L | Low | Classic pattern, well-documented |
| **Injector (manual mapping, race cond)** | L | High | Requires PE knowledge + Hyperion race timing |
| **Luau VM embedding** | M | Medium | Official Luau docs — straightforward |
| **Custom UNC API bindings** | XL | Hard | 30+ functions, need VM internals (Protos, Closures, lua_State manipulation) |
| **Scheduler integration** | XL | V. Hard | Most crashes come from here per Polycheat; need to understand yield/resume semantics |
| **Anti-detection against Hyperion** | XL | V. Hard | Constant arms race; requires: integrity bypass, IC manipulation, syscall spoofing, memory obfuscation — full-time job |
| **Decompiler/bytecode dump** | L | Medium | Unluau as base, adapt for live state |
| **Script hub + cloud sync** | M | Medium | Standard CRUD app infrastructure |
| **Key/licensing system** | S | Low | Standard licensing, but payment processor integration often rejected |
| **Multi-instance support** | M | Medium | Launch parallel Roblox processes via different data dirs |
| **Update pipeline (post-Roblox-patch)** | ongoing | Hard | Top executors update within hours; requires monitoring Roblox patches |

### Total Effort Estimate (Voltbz-class from scratch)
- **Solo dev, full-time**: 6-12 months to MVP (UNC functional but flaky)
- **Paid tier competitive**: ~24 months (stable, UNC ~90%, reliable scheduler, anti-detection that survives patches)
- **Ongoing maintenance**: Full-time+ team to keep bypass current after each Roblox update
- **Cost**: As in previous report — payment processing is a major obstacle (many processors refuse gaming exploit services)

### Key Insight from Polycheat Author (2026)
> "On platforms like modern Roblox, building a truly unrestricted executor from the ground up is no longer just about 'executing Lua', it becomes an enormous engineering project where making all the moving pieces come together reliably can take months or even years of work."

> "The workhorse category [execution] turned out to be much simpler than expected once the internal layouts were mapped correctly. Most of that time was not spent writing execution itself, but instead: reversing VM structures, fixing crashes, understanding scheduler behavior, dealing with GC traversal, debugging thread lifetime."

### Alternative Scope Reduction
Given risks identified in the previous report, possible scope reductions:
- **Open Cloud MCP**: Legitimate Luau execution via Roblox's official API (no injection, no anti-cheat cat-and-mouse)
- **Local-only executor for own games**: Use against universes you own — still ToS grey area but reduces external harm
- **Developer tool positioning**: Market as "Roblox development tool" for testing CI/CD via Luau Execution rather than "executor"

---

## 9. Sources Consulted (2026 Primary Sources)

### Open-source executor codebases
- **Polycheat** (C5Hackr): https://github.com/C5Hackr/Polycheat — VM-integrated executor (Polytoria target, not Roblox, but demonstrates integration patterns)
- **TaaprWareV2** (plusgiant5): https://github.com/plusgiant5/TaaprWareV2 — C++ DLL + C# UI, Level 8 execution, metamethod hooking
- **XenoExecutor** (xenoexecutorv1): https://github.com/xenoexecutorv1/XenoExecutor — Web Roblox executor, bytecode injection via corescript

### Luau Language Official
- **Luau Embedding Docs**: https://luau.org/api/
- **luau-lang/luau** (GitHub): https://github.com/luau-lang/luau — official source, README shows compile+load pattern
- **CMake integration**: Together with Compiler/VM subprojects
- **Discussion #2412**: "How would I inject custom globals to the Luau VM?" — global env setup pattern

### UNC (Unified Naming Convention)
- **NamingStandard** (archived May 2024): https://github.com/unified-naming-convention/NamingStandard
  - `api/scripts.md` — full Scripts API reference
  - `api/metatable.md` — full Metatable API reference
- **E-unc test suite** (sinci12): https://github.com/sinci12/E-unc — UNC environment checking script
- **some-unc-functions** (somethingsimade): https://github.com/somethingsimade/some-unc-functions

### Script Hub Libraries (2026)
- **Rayfield** (SiriusSoftwareLtd): https://github.com/SiriusSoftwareLtd/Rayfield — most adopted UI library
- **Fluent-modded** (StyearX): https://github.com/StyearX/Fluent-modded — SaveManager/InterfaceManager pattern
- **Script Examples** (lua-hub, Script-Hub.lua): loadstring + executor detection patterns

### Injection Tutorials
- **WeAreDevs**: https://wearedevs.net/forum/t/29393 — C++ DLL injection basics
- **stmxcsr**: https://stmxcsr.com/tutorials/dll-injection-createremotethread.html — CreateRemoteThread walkthrough

### Compiler/Decompiler
- **Unluau** (Atrexus): https://github.com/atrexus/unluau — Luau bytecode disassembly and decompilation

**Research Completed:** 2026-07-26
**Purpose:** Educational study of Roblox executor architecture and script construction