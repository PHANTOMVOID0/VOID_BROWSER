# 🌌 VOID BROWSER — MASTER BLUEPRINT
### *Explore Beyond.*

**Identity:** Minimal · Dark · Premium
**Version:** Master Blueprint v2.0 (Native Chromium Edition)
**Last Updated:** 2026
**Implementation Type:** NATIVE CHROMIUM FORK (C++, GN, Ninja) — NOT Electron, NOT a wrapper
**Status:** Pre-Development (Phase 0 ready to begin)

---

## ⚠️ CRITICAL NOTICE TO ANY AI OR DEVELOPER READING THIS

Before you do ANYTHING, understand these absolute rules:

1. **VOID IS A NATIVE CHROMIUM FORK.** It is written in C++, built with GN + Ninja, and uses Chromium's Views/Aura UI toolkit. It is NOT Electron, NOT CEF, NOT a WebView wrapper, NOT a webpage in a window.
2. **DO NOT SUGGEST OR USE ELECTRON, NW.JS, TAURI, CEF, OR ANY EMBEDDED FRAMEWORK.** A previous prototype attempt used Electron and failed to meet the performance and architecture goals. That path is permanently rejected.
3. **THE BASE IS `fetch chromium` VIA `depot_tools`.** You fork the full Chromium source tree and apply VOID-specific patches on top.
4. **ALL UI IS NATIVE** — built with Chromium's `views::` framework in C++, not HTML/CSS/JS in a window.
5. **INTERNAL PAGES ONLY** (like `chrome://settings`, `chrome://newtab`) use TypeScript/WebUI — this is Chromium's own pattern.
6. **THIS BLUEPRINT IS THE ONLY SOURCE OF TRUTH.** Any deviation requires updating this file FIRST.
7. **IF ANY INSTRUCTION IS UNCLEAR, STOP AND ASK.** Do not invent, guess, or substitute.

---
---
You don't need to throw away the VOID_BLUEPRINT.md. We just change the base target from raw Chromium to ungoogled-chromium.

Paste this exact response into your chat with the AI:

New Strategy Shift: Fork ungoogled-chromium instead of raw Chromium.

Directives:

Set the upstream base repository to ungoogled-chromium (Windows target).
Skip manual Phase 0 de-Googling engineering, as ungoogled-chromium already handles telemetry stripping and Google disconnections cleanly via patches.
Configure the Windows build environment (depot_tools, Visual Studio 2022, Python 3, args.gn) specifically for building an ungoogled-chromium downstream fork named VOID.
Provide the exact setup script to pull ungoogled-chromium, set up the VOID patch directory, and execute the initial native Windows compilation.
---
## 📖 TABLE OF CONTENTS

1. [Vision & Mission](#1-vision--mission)
2. [Brand Identity](#2-brand-identity)
3. [Core Philosophy](#3-core-philosophy)
4. [Implementation Rules (Hard Constraints)](#4-implementation-rules-hard-constraints)
5. [Feature Specification](#5-feature-specification)
6. [System Architecture](#6-system-architecture)
7. [Technology Stack (Native)](#7-technology-stack-native)
8. [Performance Targets](#8-performance-targets)
9. [UI/UX Specification](#9-uiux-specification)
10. [Security & Privacy Model](#10-security--privacy-model)
11. [Platform Support Matrix](#11-platform-support-matrix)
12. [Development Phases (Overview)](#12-development-phases-overview)
13. [Detailed Step-by-Step Roadmap](#13-detailed-step-by-step-roadmap)
14. [Realistic Timeline](#14-realistic-timeline)
15. [File & Folder Structure](#15-file--folder-structure)
16. [Chromium Fork Strategy](#16-chromium-fork-strategy)
17. [Keyboard Shortcuts](#17-keyboard-shortcuts)
18. [Testing Strategy](#18-testing-strategy)
19. [Release Strategy](#19-release-strategy)
20. [Project Rules](#20-project-rules)
21. [Known Risks & Mitigations](#21-known-risks--mitigations)
22. [Glossary](#22-glossary)
23. [Blueprint Checklist & Next Steps](#23-blueprint-checklist--next-steps)
24. [Handoff Instructions for Any AI](#24-handoff-instructions-for-any-ai)

---

## 1. VISION & MISSION

### Vision
To build the **world's lightest, fastest, and most private** Chromium-based browser that feels instantly familiar to Chrome users but respects their privacy, resources, and intelligence.

### Mission
VOID exists to prove that a browser can be:
- **Fast** without being bloated
- **Private** without being complicated
- **Powerful** without being heavy
- **Beautiful** without being distracting

### Target Users
- Power users tired of Chrome's RAM usage
- Privacy-conscious individuals
- Developers who need speed and DevTools
- Minimalists who hate browser bloat
- Mobile users wanting a lightweight experience

---

## 2. BRAND IDENTITY

### Name
**VOID** — represents emptiness of distraction, infinite exploration space.

### Tagline
**Explore Beyond.**

### Design Language
- **Minimal:** Nothing extra. Every pixel earns its place.
- **Dark:** Dark mode first. Light mode secondary.
- **Premium:** Smooth animations, subtle gradients, refined typography.

### Color Palette
| Role | Color | Hex |
|---|---|---|
| Primary Background | Deep Void Black | `#0A0B14` |
| Secondary Background | Soft Black | `#12141F` |
| Surface | Elevated Dark | `#1A1D2E` |
| Primary Accent | Cosmic Blue | `#6B8AFD` |
| Secondary Accent | Nebula Purple | `#A78BFA` |
| Text Primary | Pure White | `#FFFFFF` |
| Text Secondary | Dim White | `#B4B7C9` |
| Success | Mint | `#4ADE80` |
| Warning | Amber | `#FBBF24` |
| Danger | Coral | `#F87171` |
| Border | Faint Line | `#252841` |

### Typography
- **UI Font:** Inter / SF Pro / Segoe UI (platform native)
- **Mono Font:** JetBrains Mono (DevTools)

### Logo Concept
- Minimal circular void with a subtle gradient
- Works at 16px (favicon) and 512px (app icon)

---

## 3. CORE PHILOSOPHY

### The Four Pillars

1. **SPEED FIRST** — Every millisecond matters. If it slows down the browser, it doesn't ship.
2. **PRIVACY BY DEFAULT** — User data belongs to the user. Period.
3. **ZERO BLOAT** — If a feature isn't essential, it's a toggle or it's cut.
4. **FAMILIAR UX** — Chrome users should feel at home within 10 seconds.

### Non-Negotiables
- ❌ No telemetry without explicit consent
- ❌ No forced account creation
- ❌ No pre-installed partner content
- ❌ No crypto, no ads, no rewards
- ❌ No AI assistants (keep it a browser)
- ✅ Open source where possible
- ✅ User controls everything

---

## 4. IMPLEMENTATION RULES (HARD CONSTRAINTS)

These rules are **immutable**. Breaking any of them means you are not building VOID.

### Rule 1 — Native Chromium Only
- Base: Full Chromium source via `fetch chromium` and `depot_tools`
- Language: C++ (C++17/20) for all browser-process and renderer-process code
- Build system: GN (generate) + Ninja (execute) — no CMake, no Bazel, no Make
- UI toolkit: Chromium's `views::` framework (not HTML/CSS/JS for chrome UI)

### Rule 2 — No Embedded Frameworks
- ❌ Electron — rejected permanently
- ❌ NW.js — rejected
- ❌ Tauri — rejected
- ❌ CEF (Chromium Embedded Framework) — rejected
- ❌ WebView2 / WKWebView wrappers — rejected for desktop
- ✅ Only direct Chromium source fork is acceptable

### Rule 3 — Patch, Don't Rewrite
- Keep Chromium source largely intact
- Apply VOID-specific changes as patches in `//void/` directory
- Upstream Chromium security patches must merge cleanly every 4 weeks
- Never fork files Chromium changes often; use subclassing or hooks instead

### Rule 4 — WebUI for Internal Pages Only
- `chrome://newtab`, `chrome://settings`, `chrome://history`, etc. use Chromium's WebUI (TypeScript + Polymer/Lit)
- The browser chrome (tabs, omnibox, menus) is C++ native — never HTML

### Rule 5 — Zero Telemetry Means Zero
- No Google Analytics, no Measurement Protocol, no Sentry, no Crashpad uploads by default
- Every network call must be auditable and justifiable
- Crash reports exist but are 100% opt-in and routed to VOID infrastructure only

### Rule 6 — Mobile Reality
- Android: full Chromium fork is possible (`//chrome/android`)
- iOS: Apple mandates WebKit — use Chromium's iOS port (`//ios/chrome`) which already handles this. VOID iOS will be Blink-style UX wrapped around WebKit per Apple's rules.

### Rule 7 — Manifest V2 Preservation
- Chromium is removing MV2. VOID keeps it alive by maintaining the deprecated code paths in our fork.
- This is a known ongoing cost — budget for it every release cycle.

### Rule 8 — No Chrome Web Store Direct Load (Legal)
- Google's ToS may forbid third-party browsers from using Chrome Web Store directly.
- VOID provides CRX-install compatibility (any .crx file works) and will mirror the store via its own catalog if direct access is blocked.

---

## 5. FEATURE SPECIFICATION

### 5.1 Core Features (MVP — v1.0)

| ID | Feature | Priority | Source | Native Implementation Path |
|---|---|---|---|---|
| F01 | Chrome-identical UI | P0 | Chrome | Modify `//chrome/browser/ui/views/` |
| F02 | Omnibox with suggestions | P0 | Chrome | Patch `//components/omnibox/` |
| F03 | Tab management (groups, pinning) | P0 | Chrome | Use `//chrome/browser/ui/tabs/` |
| F04 | Bookmarks & bookmarks bar | P0 | Chrome | `//components/bookmarks/` |
| F05 | History & downloads | P0 | Chrome | `//components/history/`, `//components/download/` |
| F06 | Incognito mode | P0 | Chrome | Built-in, strip Google-specific hooks |
| F07 | Built-in ad blocker | P0 | Brave | Integrate `adblock-rust` as Chromium component |
| F08 | Tracker blocker | P0 | Brave | Same engine as F07 |
| F09 | Fingerprint protection | P0 | Brave | Patch Blink: Canvas, WebGL, AudioContext, Fonts |
| F10 | HTTPS auto-upgrade | P0 | Brave | Patch `//components/security_interstitials/` |
| F11 | Popup blocker | P0 | Brave | Harden `//chrome/browser/ui/blocked_content/` |
| F12 | Chrome Web Store support | P0 | Chrome | Accept CRX installs; see Rule 8 |
| F13 | Manifest V2 + V3 support | P0 | Brave | Preserve MV2 code paths in fork |
| F14 | Sleeping tabs | P0 | Edge | Extend `//components/performance_manager/` |
| F15 | Memory saver | P0 | Edge | Same subsystem |
| F16 | Efficiency mode | P0 | Edge | CPU throttling in `//base/task/` |
| F17 | Startup boost | P1 | Edge | Preload service at OS login |
| F18 | Full DevTools | P0 | Chrome | Built-in, keep intact |
| F19 | All Chrome shortcuts | P0 | Chrome | `//chrome/browser/ui/views/accelerator_table.cc` |
| F20 | Zero telemetry | P0 | Brave | Strip `//components/metrics/`, Google URLs |

### 5.2 Extended Features (Post-MVP)

| ID | Feature | Priority | Notes |
|---|---|---|---|
| F21 | Optional account login | P1 | Email or passkey |
| F22 | Encrypted bookmark sync | P1 | E2E encrypted |
| F23 | Auto-merge sync engine | P1 | Smart conflict resolution |
| F24 | Password sync | P1 | OS keychain backed |
| F25 | History sync | P2 | Optional |
| F26 | Settings sync | P2 | Optional |
| F27 | Mobile menu adaptation | P0 (mobile) | Hamburger menu on Android/iOS |
| F28 | Remote debugging | P1 | Mobile via desktop |
| F29 | Auto-updater | P0 | Silent background updates |
| F30 | Multi-profile support | P1 | Chrome-like profiles |

### 5.3 Speed Layer Features

| # | Feature | Native Path |
|---|---|---|
| S01 | DNS pre-resolve | `//net/dns/` — extend predictor |
| S02 | Connection pre-warm | `//net/socket/` — socket pool tuning |
| S03 | HTTP/3 + QUIC 0-RTT | Already in Chromium, enable aggressively |
| S04 | Resource prefetch (privacy-safe) | `//chrome/browser/prefetch/` |
| S05 | Clean network stack | Remove beacon API, tracking pixels in `//net/url_request/` |
| S06 | Speculative preconnect on hover | Omnibox + link hover handler |
| S07 | Parallel chunked downloads | `//components/download/internal/` |
| S08 | Segment heap (Windows) | Linker flag in `//build/config/win/` |

### 5.4 Explicitly Excluded Features
- ❌ AI assistant / chatbot
- ❌ Crypto wallet / rewards
- ❌ Tor integration
- ❌ Vertical tabs
- ❌ Split screen
- ❌ Sidebar apps
- ❌ Collections
- ❌ News feed
- ❌ Coupon finder
- ❌ Gaming hub
- ❌ VPN (initially)
- ❌ Reader mode (initially)

---

## 6. SYSTEM ARCHITECTURE

### 6.1 Layered Architecture

```
┌──────────────────────────────────────────────────────┐
│  LAYER 1: PRESENTATION (Native C++ Views/Aura)       │
│  - Chrome-identical UI on desktop                    │
│  - Mobile-adapted UI with hamburger menu             │
│  - Keyboard shortcuts (all Chrome bindings)          │
├──────────────────────────────────────────────────────┤
│  LAYER 2: PRIVACY SHIELD                             │
│  - Ad blocker (adblock-rust integrated as component) │
│  - Tracker blocker                                   │
│  - Fingerprint randomizer (Blink patches)            │
│  - HTTPS upgrader                                    │
│  - Popup/cookie banner blocker                       │
├──────────────────────────────────────────────────────┤
│  LAYER 3: SPEED ENGINE                               │
│  - DNS pre-resolve (net/dns)                         │
│  - Connection pre-warm (net/socket)                  │
│  - HTTP/3 + QUIC                                     │
│  - Aggressive caching                                │
│  - Resource prefetch                                 │
├──────────────────────────────────────────────────────┤
│  LAYER 4: PERFORMANCE MANAGER                        │
│  - Sleeping tabs (performance_manager)               │
│  - Memory saver                                      │
│  - Tab discarding                                    │
│  - Efficiency mode                                   │
│  - Startup boost                                     │
├──────────────────────────────────────────────────────┤
│  LAYER 5: EXTENSION HOST                             │
│  - CRX loader (Chromium built-in)                    │
│  - Manifest V2 + V3 support (MV2 preserved)          │
│  - Isolated extension processes                      │
├──────────────────────────────────────────────────────┤
│  LAYER 6: SYNC & ACCOUNT (OPTIONAL)                  │
│  - Account system (email/passkey)                    │
│  - E2E encrypted sync                                │
│  - Auto-merge engine (CRDT-based)                    │
├──────────────────────────────────────────────────────┤
│  LAYER 7: DEVTOOLS                                   │
│  - Full Chromium DevTools (kept intact)              │
│  - Remote debugging                                  │
├──────────────────────────────────────────────────────┤
│  LAYER 8: OS ABSTRACTION                             │
│  - Windows / macOS / Linux / Android / iOS           │
│  - Native integrations per platform                  │
├──────────────────────────────────────────────────────┤
│  LAYER 9: CHROMIUM CORE ENGINE (UPSTREAM)            │
│  - Blink (rendering)                                 │
│  - V8 (JavaScript)                                   │
│  - Multi-process architecture                        │
│  - Sandboxing                                        │
└──────────────────────────────────────────────────────┘
```

### 6.2 Process Model (Chromium Native)

```
Main Browser Process (C++)
├── UI Thread (views::Widget, aura::Window)
├── IO Thread
├── Network Service (sandboxed, separate process in newer Chromium)
├── Shield Engine (ad/tracker/fingerprint — in network service)
└── Sync Service (utility process)

Child Processes (Isolated, sandboxed)
├── Renderer Process (per site, Blink + V8)
├── GPU Process
├── Extension Processes (per extension)
├── Utility Processes (various services)
├── Audio Process
└── Storage Service
```

### 6.3 Data Flow (Page Load)

```
URL Input (Native omnibox - C++)
   ↓
Omnibox Resolve → Local history/bookmark suggestion (NO AI, NO REMOTE)
   ↓
Shield Check (Mojo IPC to network service)
   ↓
DNS Resolve (DoH) → Cache first
   ↓
Connection (HTTP/3 QUIC, pre-warmed)
   ↓
Request sent → Response received
   ↓
Renderer Process (Blink + V8, sandboxed)
   ↓
DOM → CSSOM → Layout → Paint → Composite
   ↓
GPU Process (Final rendering via Viz)
   ↓
Display to User (compositor delivers frames to Views)
```

### 6.4 IPC Model
- All cross-process communication uses **Mojo** (Chromium's IPC system)
- Define new Mojo interfaces for VOID-specific services (Shield, Sync)
- Never use raw IPC::Channel (deprecated)

---

## 7. TECHNOLOGY STACK (NATIVE)

### 7.1 Core Technologies (MANDATORY)

| Component | Technology | Notes |
|---|---|---|
| Base Engine | Chromium (fetch via depot_tools) | Current stable milestone |
| Primary Language | C++ (C++17/20) | Chromium standard |
| UI Framework | Chromium `views::` (Views/Aura) | Native C++ UI, NOT HTML |
| WebUI (internal pages only) | TypeScript + Lit/Polymer | Chromium WebUI pattern |
| Rendering Engine | Blink | Upstream, patched for fingerprint |
| JavaScript Engine | V8 | Upstream, unchanged |
| Build Config | GN (Generate Ninja) | Chromium standard |
| Build Executor | Ninja | Chromium standard |
| Compiler (Linux/Mac) | Clang (bundled with depot_tools) | Chromium standard |
| Compiler (Windows) | MSVC / Clang-cl | Chromium standard |
| Ad Block Engine | `adblock-rust` (Rust) | Compiled as static lib, linked via Mojo wrapper |
| IPC | Mojo | Chromium standard |

### 7.2 Supporting Technologies

| Component | Technology |
|---|---|
| Network Stack | Chromium `//net` + BoringSSL |
| Sync Backend | Rust (Axum or Actix-web) |
| Sync Protocol | gRPC + Protocol Buffers |
| Sync Database (server) | PostgreSQL (stores encrypted blobs only) |
| Sync Encryption | XChaCha20-Poly1305 (via libsodium) |
| Password Storage | OS Keychain — DPAPI (Win) / Keychain (Mac) / libsecret (Linux) |
| Mobile Android | `//chrome/android` (Java/Kotlin + C++) |
| Mobile iOS | `//ios/chrome` (Swift/Obj-C + C++, WebKit-based) |
| DevTools | Chromium DevTools (unmodified) |
| CI/CD | GitHub Actions + self-hosted runners (32GB+ RAM) |
| Version Control | Git + depot_tools |
| Distributed Build | reclient (optional, for teams) |

### 7.3 Package Formats

| Platform | Formats |
|---|---|
| Windows | `.exe` installer (NSIS), `.msi`, MSIX |
| macOS | `.dmg`, `.pkg` (signed + notarized) |
| Linux | `.deb`, `.rpm`, AppImage, Flatpak, Snap |
| Android | APK (direct), AAB (Play Store) |
| iOS | App Store, TestFlight |

### 7.4 Development Tools

| Tool | Purpose |
|---|---|
| VS Code + clangd | Primary IDE |
| CLion | Alternative IDE |
| depot_tools | Chromium fetch, sync, build |
| Clang LLDB / GDB | Debugging |
| Chromium DevTools | Web debugging |
| Lighthouse | Performance auditing |
| Wireshark | Verify zero-telemetry claim |

---

## 8. PERFORMANCE TARGETS

### 8.1 Benchmarks vs Chrome

| Metric | Chrome | VOID Target | Improvement |
|---|---|---|---|
| Cold Startup | 2-4s | **< 1.5s** | ~50% |
| Warm Startup | 1-2s | **< 0.5s** | ~70% |
| RAM (10 tabs) | 1.5-2.5 GB | **< 1 GB** | ~50% |
| RAM (30 tabs) | 4-6 GB | **< 2.5 GB** | ~55% |
| Page Load (ad-heavy) | 3-5s | **< 1.5s** | ~65% |
| Page Load (clean) | 1-2s | **< 0.8s** | ~50% |
| Install Size | ~400 MB | **< 200 MB** | ~50% |
| Battery Drain | Baseline | **-30%** | 30% better |
| Data Usage | 100% | **60-70%** | 30-40% less |
| Telemetry Calls | Many | **0** | 100% |

### 8.2 Response Time Goals

| Action | Target |
|---|---|
| Omnibox suggestion | < 10ms |
| Tab switch | < 50ms |
| New tab open | < 100ms |
| Settings page load | < 150ms |
| Shield toggle | < 20ms |
| Extension popup | < 100ms |

### 8.3 Measurement Rules
- All benchmarks run on **documented hardware** (CPU model, RAM, disk, OS version)
- At least 3 runs averaged
- Reported with min/max/median
- No claim without a reproducible test

---

## 9. UI/UX SPECIFICATION

### 9.1 Desktop Layout (Canonical)

```
┌────────────────────────────────────────────────────┐
│ [Tab1] [Tab2] [Tab3] [+]                    [─□✕] │  ← Tab bar (top)
├────────────────────────────────────────────────────┤
│ [◀][▶][🔄][🏠]  [ 🔍 Omnibox URL/search  ]       │  ← Nav bar
│                                [⭐][🧩][👤][⋮]    │
├────────────────────────────────────────────────────┤
│ [★ Bookmarks bar (toggleable)]                    │
├────────────────────────────────────────────────────┤
│                                                    │
│           WEB CONTENT AREA                         │
│                                                    │
├────────────────────────────────────────────────────┤
│ Downloads bar (when active)                        │
└────────────────────────────────────────────────────┘
```

**Implementation:** `//chrome/browser/ui/views/frame/browser_view.cc` (modified)

### 9.2 Mobile Layout (Android)

```
┌───────────────────────────┐
│ [◀] [ Omnibox ] [☰]      │  ← Top
├───────────────────────────┤
│                           │
│   WEB CONTENT             │
│                           │
├───────────────────────────┤
│  [◀] [▶] [⊞] [★] [⋮]     │  ← Bottom nav
└───────────────────────────┘
```

**Implementation:** `//chrome/android/java/src/org/chromium/chrome/browser/toolbar/`

### 9.3 New Tab Page

```
┌────────────────────────────────────┐
│                                    │
│           V O I D                  │
│                                    │
│   ┌──────────────────────────┐    │
│   │ 🔍 Search or type URL    │    │
│   └──────────────────────────┘    │
│                                    │
│   [■] [■] [■] [■] [■] [+]        │
│                                    │
│   🛡️ 1,234 ads blocked today     │
│                                    │
└────────────────────────────────────┘
```

**Implementation:** WebUI at `chrome://newtab` — TypeScript + Lit

### 9.4 Shield Panel

```
┌──────────────────────────┐
│ 🛡️ VOID Shields          │
│ ━━━━━━━━━━━━━━━━━━━━━━━ │
│ On this site:            │
│   Ads blocked:      23   │
│   Trackers blocked:  8   │
│                          │
│ [●] Block Ads            │
│ [●] Block Trackers       │
│ [●] Block Fingerprinting │
│ [●] Force HTTPS          │
│                          │
│ [ Shields OFF for site ] │
└──────────────────────────┘
```

**Implementation:** Native bubble (`views::BubbleDialogDelegateView`) anchored to omnibox icon

### 9.5 UI Component Checklist (Native Views)

- [ ] Window frame — `BrowserFrame`
- [ ] Tab bar — `TabStrip` (color-coded groups)
- [ ] Omnibox — `OmniboxView`
- [ ] Toolbar — `ToolbarView`
- [ ] 3-dot menu — `AppMenu`
- [ ] Settings page — WebUI (`chrome://settings`)
- [ ] Bookmarks bar — `BookmarkBarView`
- [ ] Downloads bar — `DownloadShelfView`
- [ ] Find bar — `FindBarView`
- [ ] Profile switcher — `AvatarToolbarButton`
- [ ] Theme engine — `ThemeService`

---

## 10. SECURITY & PRIVACY MODEL

### 10.1 Default Settings

| Setting | Default |
|---|---|
| Telemetry | OFF (hardcoded, no toggle to enable) |
| Crash reports | OFF (opt-in only) |
| Usage statistics | OFF |
| Ad blocking | ON |
| Tracker blocking | ON |
| Fingerprint protection | ON |
| HTTPS upgrade | ON |
| Third-party cookies | BLOCKED |
| DNS over HTTPS | ON |
| Safe Browsing | ON (local DB; no Google calls) |
| Autofill | OFF (opt-in) |
| Password save prompts | ON |

### 10.2 Data Collection Policy

**VOID collects: NOTHING.**

Exceptions (all opt-in):
- Crash reports (user must explicitly enable)
- Sync data (encrypted, if user signs in)

### 10.3 Sync Security

- E2E encryption: XChaCha20-Poly1305 (via libsodium)
- Zero-knowledge server design
- User-controlled encryption keys (derived from passphrase via Argon2id)
- Server stores ciphertext only — cannot read any synced data
- Optional self-hosting (publish server as open source)
- Encryption happens client-side BEFORE upload, always

### 10.4 Sandbox & Isolation
- All renderer processes sandboxed (Chromium default)
- Site isolation enabled by default
- Extension processes isolated per extension

---

## 11. PLATFORM SUPPORT MATRIX

| Platform | Priority | Release | Notes |
|---|---|---|---|
| Windows 10/11 (x64) | P0 | v1.0 | MSVC build |
| macOS 11+ (Universal) | P0 | v1.0 | ARM64 + x64 |
| Linux (Ubuntu, Fedora, Arch) | P0 | v1.0 | Clang build |
| Android 8+ | P1 | v1.1 | `//chrome/android` |
| iOS 15+ | P2 | v1.2 | `//ios/chrome` (WebKit, per Apple) |
| Linux ARM | P2 | v1.2 | Cross-compile |
| Windows ARM | P3 | Future | |

---

## 12. DEVELOPMENT PHASES (OVERVIEW)

### Phase 0 — Foundation (Weeks 1-4)
Set up build environment, fetch Chromium, first successful build, strip Google services, rebrand to VOID.

### Phase 1 — UI Clone (Weeks 5-10)
Chrome-identical desktop UI (native Views), mobile-adapted UI, all keyboard shortcuts, dark/light theme.

### Phase 2 — Privacy Shield (Weeks 11-16)
Integrate adblock-rust, tracker blocker, fingerprint randomizer (Blink patches), HTTPS upgrader, Shield UI (native bubble).

### Phase 3 — Performance (Weeks 17-22)
Sleeping tabs, memory saver, speed layer (DNS prewarm, QUIC), clean network stack.

### Phase 4 — Extensions (Weeks 23-26)
CRX install support, MV2 preservation, MV3 compat, extension testing.

### Phase 5 — Sync (Weeks 27-32)
Account system, E2E encrypted sync, auto-merge engine.

### Phase 6 — DevTools & Cross-Platform (Weeks 33-38)
Full DevTools on all platforms, Win/Mac/Linux builds, Android build, iOS build.

### Phase 7 — Polish & Launch (Weeks 39-44)
Security audit, performance tuning, beta release, v1.0 launch.

**Solo estimate: ~24 months. Small team (3-5): ~11 months.**

---

## 13. DETAILED STEP-BY-STEP ROADMAP

### PHASE 0: SETUP & INFRASTRUCTURE (Weeks 1-4)

| Step | Task | Native Details |
|---|---|---|
| 0.1 | Prepare build machine | 32GB+ RAM, 100GB+ SSD, 8+ cores, Linux preferred |
| 0.2 | Install `depot_tools` | `git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git` |
| 0.3 | Add depot_tools to PATH | Required for `fetch`, `gn`, `ninja` |
| 0.4 | Fetch Chromium source | `fetch --nohooks chromium` (~30GB download, ~100GB extracted) |
| 0.5 | Install build dependencies | `./build/install-build-deps.sh` (Linux) |
| 0.6 | Run `gclient sync` | Downloads sub-dependencies |
| 0.7 | Generate build files | `gn gen out/Default` with args: `is_debug=false is_component_build=true symbol_level=1` |
| 0.8 | First build | `autoninja -C out/Default chrome` (2-8 hours first time) |
| 0.9 | Run vanilla Chromium | Verify `./out/Default/chrome` launches |
| 0.10 | Create VOID repo | Separate Git repo for VOID patches |
| 0.11 | Create `//void/` directory | VOID-specific C++ source lives here |
| 0.12 | Strip Google services | Remove `//google_apis/`, blank out API keys, disable Sync, disable UMA |
| 0.13 | Rename everything | Product name, process name, user agent, binary name |
| 0.14 | Rebrand assets | Replace all Chromium icons/branding with VOID |
| 0.15 | Verify clean build | `autoninja -C out/Default void` (new target) |

### PHASE 1: CHROME UI CLONE (Weeks 5-10)
(Native Views implementation — not HTML)

| Step | Task | File Location |
|---|---|---|
| 1.1 | Customize window frame | `//chrome/browser/ui/views/frame/browser_frame.cc` |
| 1.2 | Tab bar styling | `//chrome/browser/ui/views/tabs/tab.cc` |
| 1.3 | Tab groups | `//chrome/browser/ui/views/tabs/tab_group_header.cc` |
| 1.4 | Omnibox styling | `//components/omnibox/browser/` + `//chrome/browser/ui/views/omnibox/` |
| 1.5 | Toolbar buttons | `//chrome/browser/ui/views/toolbar/toolbar_view.cc` |
| 1.6 | 3-dot menu | `//chrome/browser/ui/views/toolbar/app_menu.cc` |
| 1.7 | New Tab Page | `//chrome/browser/resources/new_tab_page/` (WebUI, TypeScript) |
| 1.8 | Settings page | `//chrome/browser/resources/settings/` (WebUI, TypeScript) |
| 1.9 | Bookmarks bar | `//chrome/browser/ui/views/bookmarks/bookmark_bar_view.cc` |
| 1.10 | Downloads bar | `//chrome/browser/ui/views/download/download_shelf_view.cc` |
| 1.11 | Find in page | `//chrome/browser/ui/views/find_bar_host.cc` |
| 1.12 | Profile switcher | `//chrome/browser/ui/views/profiles/avatar_toolbar_button.cc` |
| 1.13 | Dark/Light mode | `//chrome/browser/themes/theme_service.cc` |
| 1.14 | Mobile UI (Android) | `//chrome/android/java/src/org/chromium/chrome/browser/toolbar/` |
| 1.15 | Keyboard shortcuts | `//chrome/browser/ui/views/accelerator_table.cc` |

### PHASE 2: AD BLOCKER + PRIVACY SHIELD (Weeks 11-16)

| Step | Task | Details |
|---|---|---|
| 2.1 | Vendor adblock-rust | Add as `//third_party/adblock_rust/` |
| 2.2 | Build Rust static lib | Use `cargo` invocation in GN |
| 2.3 | Create Mojo interface | `//void/shield/shield.mojom` |
| 2.4 | Integrate in network service | Hook `URLLoader::Start()` in `//services/network/` |
| 2.5 | Load filter lists | Bundle EasyList, EasyPrivacy at build time |
| 2.6 | Cosmetic filtering | Inject CSS via `ContentSettingsAgent` |
| 2.7 | Fingerprint randomization | Patch Blink: `CanvasRenderingContext2D`, `WebGLRenderingContext`, `AudioContext`, font enumeration |
| 2.8 | HTTPS auto-upgrade | Patch `//components/security_interstitials/content/https_only_mode_navigation_throttle.cc` |
| 2.9 | Popup blocker | Harden `//chrome/browser/ui/blocked_content/` |
| 2.10 | Cookie banner hider | Cosmetic filter list integration |
| 2.11 | Per-site Shield toggle | New `ContentSetting` type |
| 2.12 | Shield stats dashboard | Native bubble + chrome://shields page |
| 2.13 | Filter auto-update | Background delta download every 24h |

### PHASE 3: PERFORMANCE ENGINE (Weeks 17-22)

| Step | Task | File Location |
|---|---|---|
| 3.1 | Sleeping Tabs | Extend `//components/performance_manager/` |
| 3.2 | Memory Saver | Same subsystem |
| 3.3 | Tab Discarding | `TabLifecycleUnitExternal` |
| 3.4 | Efficiency Mode | `//base/task/thread_pool/` priority tuning |
| 3.5 | Startup Boost | Platform-specific preload service |
| 3.6 | DNS Pre-resolve | `//net/dns/dns_config_service.cc` + predictor |
| 3.7 | Connection Pre-warm | `//net/socket/client_socket_pool_manager.cc` |
| 3.8 | HTTP/3 + QUIC | Enable via GN flag `enable_quic=true` |
| 3.9 | Resource Prefetch | `//chrome/browser/prefetch/` |
| 3.10 | Clean Network Stack | Audit & remove all Google URLs in `//chrome/common/url_constants.cc` |
| 3.11 | Aggressive Caching | Tune `//net/disk_cache/` limits |
| 3.12 | Parallel Downloads | `//components/download/internal/common/parallel_download_job.cc` |
| 3.13 | Segment Heap (Win) | `//build/config/win/BUILD.gn` linker flag |
| 3.14 | Strip unused code | Disable: Google Pay, Cast, Smart Lock, Chromecast, Chrome OS features |

### PHASE 4: EXTENSIONS (Weeks 23-26)

| Step | Task | Details |
|---|---|---|
| 4.1 | Keep extension system enabled | `enable_extensions=true` in GN args |
| 4.2 | CRX install path | `//chrome/browser/extensions/crx_installer.cc` — accept any valid CRX |
| 4.3 | Preserve MV2 code | Do NOT apply upstream patches that remove MV2 |
| 4.4 | Full MV3 support | Keep upstream MV3 as-is |
| 4.5 | Extension isolation | Already per-extension process in Chromium |
| 4.6 | Extension UI (puzzle icon) | `//chrome/browser/ui/views/extensions/` |
| 4.7 | Test top 50 extensions | uBlock Origin (MV2 + MV3), Bitwarden, Dark Reader, etc. |

### PHASE 5: ACCOUNT + SYNC (Weeks 27-32)

| Step | Task | Details |
|---|---|---|
| 5.1 | Account system | Email + passkey (WebAuthn) |
| 5.2 | E2E encryption | libsodium XChaCha20-Poly1305 |
| 5.3 | Sync server | Rust (Axum) + PostgreSQL |
| 5.4 | Bookmark sync | CRDT-based merge (Yjs or Automerge concepts) |
| 5.5 | Auto-merge engine | Timestamp + device priority + CRDT |
| 5.6 | Password sync | OS keychain backed, encrypted blob synced |
| 5.7 | History sync | Optional |
| 5.8 | Settings sync | Optional |
| 5.9 | Offline mode | Queue changes, sync when online |
| 5.10 | Device management | Chrome://sync-devices equivalent |

### PHASE 6: DEVTOOLS + CROSS-PLATFORM (Weeks 33-38)

| Step | Task | Details |
|---|---|---|
| 6.1 | DevTools | Already built-in; don't remove |
| 6.2 | All panels | Verify Elements/Console/Network/Sources/Perf/Memory/App/Lighthouse work |
| 6.3 | Mobile remote debug | `chrome://inspect` → USB/WiFi devices |
| 6.4 | Windows build | NSIS installer, Fluent theme, Taskbar integration |
| 6.5 | macOS build | DMG, codesign + notarize |
| 6.6 | Linux build | DEB, RPM, AppImage, Flatpak, Snap |
| 6.7 | Android build | APK + AAB, Play Store listing |
| 6.8 | iOS build | TestFlight → App Store (WebKit mandatory) |
| 6.9 | Auto-updater | Omaha protocol (Chromium's) or custom — signed, delta |
| 6.10 | OS notifications | Platform-native API per OS |

### PHASE 7: POLISH + LAUNCH (Weeks 39-44)

| Step | Task |
|---|---|
| 7.1 | External security audit |
| 7.2 | Performance benchmarks (Speedometer, JetStream, MotionMark) |
| 7.3 | Compatibility testing (top 10,000 sites) |
| 7.4 | Accessibility audit (NVDA, VoiceOver, keyboard nav) |
| 7.5 | Localization (start with 10 languages) |
| 7.6 | Installer polish |
| 7.7 | Website + branding + download page |
| 7.8 | Beta release — public |
| 7.9 | Bug fixes (2-4 weeks) |
| 7.10 | **v1.0 LAUNCH** 🎉 |

---

## 14. REALISTIC TIMELINE

| Phase | Duration | Difficulty |
|---|---|---|
| Phase 0: Setup | 4 weeks | ⭐⭐⭐ |
| Phase 1: UI Clone | 6 weeks | ⭐⭐⭐⭐ |
| Phase 2: Ad Block | 6 weeks | ⭐⭐⭐ |
| Phase 3: Performance | 6 weeks | ⭐⭐⭐⭐⭐ |
| Phase 4: Extensions | 4 weeks | ⭐⭐⭐ |
| Phase 5: Sync | 6 weeks | ⭐⭐⭐⭐ |
| Phase 6: DevTools + OS | 6 weeks | ⭐⭐⭐⭐ |
| Phase 7: Polish | 6 weeks | ⭐⭐⭐ |
| **TOTAL** | **~44 weeks (~11 months)** | **Small team** |

> ⚠️ **Solo developer: ~24 months** — this is a massive native codebase.
> ⚠️ **Team of 3-5: ~11 months** realistic.

---

## 15. FILE & FOLDER STRUCTURE

```
void-browser/
├── README.md
├── VOID_BLUEPRINT.md          ← This file (source of truth)
├── LICENSE
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
│
├── chromium/                  ← Chromium source via depot_tools
│   └── src/                   ← The full Chromium tree
│       └── void/              ← VOID-specific C++ code (NEW)
│           ├── branding/
│           │   ├── logos/
│           │   ├── icons/
│           │   └── theme/
│           │
│           ├── shield/        ← Privacy shield (native C++)
│           │   ├── adblock/   ← adblock-rust FFI wrapper
│           │   ├── trackers/
│           │   ├── fingerprint/
│           │   └── https/
│           │
│           ├── speed/         ← Performance layer
│           │   ├── dns_prewarm/
│           │   ├── connection_pool/
│           │   └── prefetch/
│           │
│           ├── performance/
│           │   ├── sleeping_tabs/
│           │   ├── memory_saver/
│           │   └── efficiency_mode/
│           │
│           ├── sync_client/   ← Sync client-side (C++)
│           │   ├── crypto/
│           │   └── merge/
│           │
│           └── ui/            ← VOID-specific Views patches
│               ├── desktop/
│               └── mobile/
│
├── patches/                   ← VOID patches on top of Chromium
│   ├── 0001-branding.patch
│   ├── 0002-strip-google.patch
│   └── ...
│
├── server/                    ← Sync server (Rust)
│   ├── src/
│   │   ├── api/
│   │   ├── db/
│   │   └── crypto/
│   └── Cargo.toml
│
├── third_party/
│   └── adblock_rust/          ← Vendored adblock-rust
│
├── docs/
│   ├── architecture/
│   ├── build/
│   ├── guides/
│   └── api/
│
├── scripts/
│   ├── setup.sh               ← depot_tools + fetch
│   ├── build.sh               ← Build VOID
│   ├── package.sh             ← Create installers
│   └── apply_patches.sh       ← Apply VOID patches to Chromium
│
└── .github/
    └── workflows/
        ├── build-linux.yml
        ├── build-windows.yml
        └── build-macos.yml
```

---

## 16. CHROMIUM FORK STRATEGY

### 16.1 Upstream Sync Cadence
- **Every 4 weeks:** merge latest stable Chromium milestone
- **Security patches:** merge within 72 hours of upstream release
- **Automated conflict detection:** CI runs rebase dry-run nightly

### 16.2 Patch Discipline
- All VOID changes live in `//void/` where possible
- When modifying upstream files, use smallest possible diff
- Prefer subclassing / hooks / observers over direct edits
- Every patch has a comment: `// VOID: [reason for change]`

### 16.3 What NOT to Fork
- V8 (use upstream)
- Blink core (patch only for fingerprint; no deep forks)
- Skia
- Mojo

### 16.4 What WE Fork
- Branding assets
- Default settings (`chrome://flags` defaults)
- Metrics code (deleted)
- Google-specific service code (deleted)
- Tab/toolbar styling (visual-only changes)

### 16.5 Patch Management Tool
- Use `git am` for patch application
- Maintain `patches/series` file listing patch order
- Consider using `quilt` or `git-series` for large patch sets

---

## 17. KEYBOARD SHORTCUTS

**All Chrome shortcuts preserved exactly.**

Implementation: `//chrome/browser/ui/views/accelerator_table.cc`

### Tab Management
| Shortcut | Action |
|---|---|
| `Ctrl+T` | New tab |
| `Ctrl+W` / `Ctrl+F4` | Close tab |
| `Ctrl+Shift+T` | Reopen closed tab |
| `Ctrl+Tab` | Next tab |
| `Ctrl+Shift+Tab` | Previous tab |
| `Ctrl+1-8` | Jump to tab 1-8 |
| `Ctrl+9` | Last tab |
| `Ctrl+N` | New window |
| `Ctrl+Shift+N` | New incognito window |

### Navigation
| Shortcut | Action |
|---|---|
| `Ctrl+L` / `F6` / `Alt+D` | Focus omnibox |
| `Alt+Left` | Back |
| `Alt+Right` | Forward |
| `F5` / `Ctrl+R` | Reload |
| `Ctrl+F5` / `Ctrl+Shift+R` | Hard reload |
| `Esc` | Stop loading |

### Page
| Shortcut | Action |
|---|---|
| `Ctrl+F` | Find in page |
| `Ctrl+D` | Bookmark page |
| `Ctrl+Shift+D` | Bookmark all tabs |
| `Ctrl+P` | Print |
| `Ctrl+S` | Save page |
| `Ctrl++` / `Ctrl+-` | Zoom in/out |
| `Ctrl+0` | Reset zoom |
| `F11` | Fullscreen |

### Developer
| Shortcut | Action |
|---|---|
| `F12` / `Ctrl+Shift+I` | DevTools |
| `Ctrl+Shift+J` | DevTools Console |
| `Ctrl+Shift+C` | Inspect element |
| `Ctrl+U` | View source |

### History & Downloads
| Shortcut | Action |
|---|---|
| `Ctrl+H` | History |
| `Ctrl+J` | Downloads |
| `Ctrl+Shift+Del` | Clear browsing data |

---

## 18. TESTING STRATEGY

### 18.1 Test Levels

| Level | Tool | Coverage |
|---|---|---|
| Unit | GTest (`//base/test/`) | Core logic, shield engine |
| Browser | Chromium `browser_tests` | Component interactions |
| E2E | Telemetry / Puppeteer | Full user flows |
| Performance | Speedometer, JetStream, MotionMark | Benchmark tracking |
| Compat | Top 10,000 sites | Weekly automated runs |
| Security | OWASP ZAP, manual pentest, fuzzing | Pre-release |
| Network | Wireshark capture | Prove zero telemetry claim |

### 18.2 Quality Gates
- No PR merges without passing `browser_tests`
- Performance regression >5% blocks release
- Any new network call requires privacy review + Wireshark proof
- Security audit before each major release

### 18.3 Zero-Telemetry Verification
- Monthly Wireshark capture during 1-hour browsing session
- All outgoing connections must be to user-requested destinations OR explicitly documented VOID endpoints (sync, update)
- Any unexpected call = release blocker

---

## 19. RELEASE STRATEGY

### Release Channels

| Channel | Frequency | Audience |
|---|---|---|
| Nightly | Daily | Developers |
| Beta | Weekly | Early adopters |
| Stable | Monthly | Public |

### Versioning
- **Semantic Versioning**: `MAJOR.MINOR.PATCH`
- Example: `1.0.0`, `1.1.0`, `1.1.1`

### Update Mechanism
- Omaha-style update protocol (or custom equivalent)
- Signed binaries only (code signing cert required)
- Delta updates (bsdiff/courgette) for small downloads
- User can pause/disable auto-update

### Build Signing Requirements
- **Windows:** EV code signing cert (Authenticode)
- **macOS:** Apple Developer ID + notarization
- **Linux:** GPG-signed .deb/.rpm; Flathub/Snapcraft for their stores

---

## 20. PROJECT RULES

### Development Rules
1. **No feature without a performance budget**
2. **No network call without privacy review + Wireshark proof**
3. **No UI change without matching Chrome unless justified**
4. **No dependencies that collect telemetry**
5. **Code must be reviewed by at least 1 person**
6. **All commits signed (GPG)**
7. **Breaking changes require RFC document**
8. **Every VOID-modified upstream file gets a `// VOID:` comment explaining why**

### Design Rules
1. **Dark mode first**
2. **Minimum 60fps animations (test with DevTools Performance)**
3. **No feature requires more than 3 clicks**
4. **Mobile UX never "desktop shrunk"**
5. **Every desktop feature must have a keyboard shortcut**
6. **Mobile equivalents are gestures**

### Privacy Rules
1. **Nothing leaves the device without user knowledge**
2. **Opt-in, never opt-out for any data collection**
3. **Clear language — no dark patterns**
4. **Full data export always available**
5. **Full data deletion always available**

### Native Code Rules
1. **No HTML/CSS/JS for browser chrome UI** (only for WebUI pages)
2. **All IPC via Mojo**
3. **Use `base::` types (`base::string16`, `base::FilePath`) — not raw STL where Chromium has equivalents**
4. **Follow Chromium's C++ style guide**
5. **Use `scoped_refptr` and smart pointers — no raw `new`/`delete`**

---

## 21. KNOWN RISKS & MITIGATIONS

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R1 | Chromium removes MV2 code entirely | High | High | Maintain MV2 patches in fork; fork extension system if needed |
| R2 | Chrome Web Store blocks third-party browsers | Medium | High | Support CRX sideload; build own catalog/mirror |
| R3 | iOS Apple WebKit mandate limits features | Certain | Medium | Use `//ios/chrome`; document feature gaps on iOS |
| R4 | Chromium upstream merge conflicts | High (ongoing) | Medium | Discipline: keep patches small, isolated in `//void/` |
| R5 | Trademark / trade-dress issues (Chrome lookalike) | Medium | High | Distinct branding/colors; avoid logos/names resembling Chrome |
| R6 | Build machine too weak | High | High | Require 32GB+ RAM / 100GB+ SSD; use reclient if team |
| R7 | Solo developer burnout | High | High | Budget 24+ months; clear milestones; MVP scope discipline |
| R8 | Security vulnerability not patched in time | Medium | Critical | 72-hour SLA on upstream security patches; automated merge testing |
| R9 | Signing cert cost (EV cert ~$300-500/yr) | Certain | Low | Budget for it; required for Windows SmartScreen reputation |
| R10 | Apple App Store rejection | Medium | Medium | Follow all guidelines; have beta channel ready via TestFlight |

---

## 22. GLOSSARY

| Term | Meaning |
|---|---|
| **Blink** | Chromium's rendering engine |
| **V8** | Chromium's JavaScript engine |
| **Chromium** | Open-source browser project by Google |
| **Omnibox** | Combined URL + search bar |
| **Shield** | VOID's privacy protection system |
| **adblock-rust** | Brave's open-source ad blocking library |
| **Manifest V2/V3** | Chrome extension API versions |
| **E2E** | End-to-end encryption |
| **DoH** | DNS over HTTPS |
| **QUIC** | HTTP/3 transport protocol |
| **Sandbox** | Isolated process for security |
| **GN** | Chromium's build configuration tool |
| **Ninja** | Chromium's build execution tool |
| **depot_tools** | Google's Chromium developer toolchain |
| **Views** | Chromium's native C++ UI toolkit |
| **Aura** | Chromium's windowing abstraction |
| **Mojo** | Chromium's IPC system |
| **WebUI** | Internal browser pages (chrome://*) built with web tech |
| **CRX** | Chrome extension package format |
| **Omaha** | Google's update protocol (open source) |
| **CRDT** | Conflict-free Replicated Data Type (for sync merging) |
| **reclient** | Distributed build system for Chromium |

---

## 23. BLUEPRINT CHECKLIST & NEXT STEPS

Before writing a single line of code, confirm:

- [x] Name chosen: **VOID**
- [x] Tagline: **Explore Beyond.**
- [x] Identity: Minimal · Dark · Premium
- [x] Implementation type: **NATIVE CHROMIUM FORK (C++, GN, Ninja)**
- [x] Electron and all wrappers explicitly rejected
- [x] Core features locked
- [x] Excluded features documented
- [x] Architecture designed (9 layers)
- [x] Tech stack chosen (all native)
- [x] Performance targets set with measurement rules
- [x] UI/UX specified (native Views + WebUI for internal pages)
- [x] Privacy model defined (zero telemetry hardcoded)
- [x] Platforms prioritized
- [x] Development phases outlined
- [x] File structure planned (Chromium tree + //void/)
- [x] Fork strategy defined
- [x] Shortcuts documented
- [x] Testing strategy set (including Wireshark proof)
- [x] Release strategy planned (signing, Omaha updates)
- [x] Project rules established (native code rules included)
- [x] Risks identified with mitigations
- [x] Handoff instructions written

### 🚀 Next Step
Proceed to **Phase 0: Foundation Setup**
1. Provision build machine (32GB+ RAM, 100GB+ SSD, Linux recommended)
2. Install depot_tools
3. `fetch chromium`
4. First vanilla build
5. Strip Google services
6. Rebrand to VOID

### How to use this file
1. Create folder: `void-browser/`
2. Save as `VOID_BLUEPRINT.md`
3. `git init` and commit as first commit
4. **Never deviate without updating this blueprint first**

---

## 24. HANDOFF INSTRUCTIONS FOR ANY AI

If you are an AI reading this blueprint to help build VOID, follow these rules **absolutely**:

### MUST DO
1. ✅ Read sections 1-24 completely before acting
2. ✅ Treat Section 4 (Implementation Rules) as **non-negotiable law**
3. ✅ Assume all work is on **native Chromium C++ source**
4. ✅ Reference file paths relative to Chromium checkout (e.g., `//chrome/browser/ui/views/`)
5. ✅ When adding new C++ code, place it under `//void/`
6. ✅ When patching upstream files, add `// VOID: [reason]` comment
7. ✅ Confirm the current Phase (0-7) before suggesting work
8. ✅ Produce code that compiles with GN + Ninja
9. ✅ Follow Chromium C++ style guide
10. ✅ If instruction is ambiguous, STOP and ask the user

### MUST NOT DO
1. ❌ Do NOT suggest Electron, NW.js, Tauri, CEF, or any wrapper
2. ❌ Do NOT write browser chrome UI in HTML/CSS/JS (only WebUI internal pages)
3. ❌ Do NOT use CMake, Bazel, or Make — only GN + Ninja
4. ❌ Do NOT add telemetry, analytics, or crash reporting without opt-in
5. ❌ Do NOT add dependencies that phone home
6. ❌ Do NOT invent file paths — check Chromium source tree
7. ❌ Do NOT skip tests — every PR needs `browser_tests`
8. ❌ Do NOT claim features complete without Wireshark verification of zero-telemetry
9. ❌ Do NOT propose a "simpler" alternative to native Chromium (none exists for this scope)
10. ❌ Do NOT change this blueprint without the user's explicit approval

### Prompt Template to Give Another AI

```
I'm building VOID Browser, a native Chromium fork.
Read the attached VOID_BLUEPRINT.md completely.

RULES:
- Native Chromium only (C++, GN, Ninja)
- No Electron, no wrappers, no HTML UI for browser chrome
- WebUI is only for chrome:// internal pages
- Follow Section 4 Implementation Rules absolutely
- Place new code under //void/
- Mark patches with // VOID: comment

Current phase: [PHASE NUMBER]
Current task: [TASK ID from Section 13]

Help me implement [SPECIFIC STEP].
Show me:
1. Exact file(s) to create or modify
2. Full C++ code (not pseudocode)
3. GN build file changes
4. How to test it
5. How to verify zero-telemetry is preserved
```

---

**VOID BROWSER — Explore Beyond.**

*This document is the single source of truth. Any deviation requires updating this blueprint first.*

*Built native. Built private. Built fast.*

---
