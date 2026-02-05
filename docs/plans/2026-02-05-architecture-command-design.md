# Architecture Command Design

**Date:** 2026-02-05
**Status:** Approved
**Author:** Francesco Farruggia + Claude

---

## Overview

Design for `/create-architecture` and `/create-service-manager` commands that generate comprehensive, AI-friendly architecture documentation for multi-platform applications.

## Confirmed Decisions

| # | Area | Decision |
|---|------|----------|
| 1 | Command structure | Hybrid — single entry point, dynamic sub-agents |
| 2 | Platforms | iOS, Android, Next.js/React, Roku, Lightning, Fire TV VEGA, LG, Samsung, Comcast |
| 3 | Research | Quick official sources first, smart deep-dive if gaps detected |
| 4 | Confidence display | Category checklist with percentage values |
| 5 | Output structure | Hierarchical with sub-indexes |
| 6 | Service managers | Core set + conditional + `/create-service-manager` command |
| 7 | Orchestration | Platform-dependent, mandatory, easy to modify |
| 8 | Template engine | Hybrid — build-time core + server-configurable zones |
| 9 | Data model | Layered: NetworkDTO → DomainModel → ViewState |
| 10 | Error handling | Local handling + central escalation |
| 11 | Telemetry | Tiered with sampling, remote deep-capture, extensible |
| 12 | Event bus | Typed events + request/response, platform-specific implementation |
| 13 | Database | Versioned with migration/rollback, platform-specific tech |
| 14 | Testing | Full architecture + platform-specific tooling |
| 15 | App type | Natural inference + domain question banks |
| 16 | AI compliance | YAML manifest with verification rules |
| 17 | UX | Conversational + confidence summaries |

---

## Supported Platforms

- `ios` — iPhone, iPad, Apple TV, Mac Catalyst (Swift/SwiftUI)
- `android` — Mobile, Android TV (Kotlin)
- `web` — Next.js, React, Node.js
- `roku` — BrightScript/SceneGraph
- `lightning` — LG WebOS, Samsung Tizen
- `firetv-vega` — Amazon Fire TV VEGA
- `comcast` — X1, Flex, Sky

---

## Sub-Agent Architecture

| Agent | Trigger | Purpose |
|-------|---------|---------|
| `platform-research-agent` | Immediately on start | Gathers official docs, guidelines, latest APIs |
| `domain-research-agent` | After app type detected | Researches domain-specific patterns |
| `best-practices-agent` | After platform + domain known | SOLID, DI, state management patterns |
| `architecture-writer-agent` | At generation time | Produces the markdown files |
| `compliance-validator-agent` | Post-generation | Validates structure and manifest |

---

## Confidence Categories

| Category | Weight | Min to Proceed |
|----------|--------|----------------|
| Platform & Targets | 15% | 90% |
| App Type & Domain | 20% | 85% |
| Content/Data Strategy | 15% | 75% |
| Features & Scope | 20% | 80% |
| Technical Constraints | 15% | 70% |
| Integration & APIs | 10% | 60% |
| Non-Functional | 5% | 50% |

**Ready to generate:** overall ≥ 95% AND all minimums met

---

## Output Structure

```
Architecture/<platform>/<app-type>/
├── MANIFEST.yaml
├── INDEX.md
├── 01-foundation/
│   ├── INDEX.md
│   ├── principles.md
│   ├── patterns.md
│   └── glossary.md
├── 02-core-services/
│   ├── INDEX.md
│   ├── service-manager-base.md
│   ├── orchestration-engine.md
│   ├── template-engine.md
│   ├── event-bus.md
│   ├── state-manager.md
│   ├── network-manager.md
│   ├── data-manager.md
│   ├── error-manager.md
│   └── telemetry-manager.md
├── 03-domain-services/
│   ├── INDEX.md
│   └── [domain-specific-managers].md
├── 04-data-layer/
│   ├── INDEX.md
│   ├── models/
│   ├── transformers.md
│   ├── persistence.md
│   └── migration.md
├── 05-ui-layer/
│   ├── INDEX.md
│   ├── template-system.md
│   ├── component-catalog.md
│   └── navigation.md
├── 06-implementation/
│   ├── INDEX.md
│   ├── setup-guide.md
│   ├── service-manager-template.md
│   ├── bootstrap-sequence.md
│   └── examples/
├── 07-testing/
│   ├── INDEX.md
│   ├── strategy.md
│   ├── unit-tests.md
│   ├── integration-tests.md
│   ├── mocking.md
│   └── ci-integration.md
└── 08-appendix/
    ├── INDEX.md
    ├── platform-specifics.md
    ├── certification-checklist.md
    └── references.md
```

---

## Core Service Managers

### Always Generated
1. **OrchestrationManager** — App flow coordination
2. **TemplateManager** — UI rendering
3. **NetworkManager** — API layer
4. **DataManager** — Persistence & transformation
5. **StateManager** — Single source of truth
6. **TelemetryManager** — Instrumentation
7. **ErrorManager** — Error handling & degradation
8. **EventBusManager** — Inter-module communication

### Conditional (based on app type)
- **PlayerManager** — Video playback (OTT)
- **DRMManager** — Content protection (OTT)
- **AdManager** — Ad insertion (OTT)
- **AuthManager** — Authentication
- **OfflineManager** — Downloads
- **NavigationManager** — Routing

---

## AI Compliance System

### MANIFEST.yaml Structure
```yaml
version: "1.0"
compliance:
  required_reading:
    - path: "file.md"
      priority: 1
      verify: "Verification question"
  conditional_reading:
    - trigger: "context"
      required: ["files"]
  implementation_rules:
    - id: "RULE-001"
      rule: "Description"
      severity: "critical|high|medium"
```

### Enforcement Rules
- SVC-001: All services MUST extend BaseServiceManager
- SVC-002: Services MUST NOT communicate directly — use EventBus
- NET-001: All network calls MUST go through NetworkManager
- DATA-001: Remote data MUST be transformed to internal models
- UI-001: UI rendering MUST use TemplateManager
- ERR-001: Errors MUST be handled locally or escalated
- TEL-001: All user actions MUST emit telemetry events

---

## Data Layer

### Transformation Chain
```
Remote API → NetworkDTO → DomainModel → ViewState → UI
```

### Platform Storage Options
| Platform | Storage | Secure Storage |
|----------|---------|----------------|
| iOS/tvOS | SwiftData, Core Data, SQLite | Keychain |
| Android | Room (SQLite) | EncryptedSharedPreferences |
| Web | IndexedDB, localStorage | httpOnly cookies |
| Roku | Registry sections | N/A |
| Lightning | localStorage | N/A |

---

## Event Bus Patterns

| Pattern | Use Case | Example |
|---------|----------|---------|
| Event | Fire and forget | `UserDidLogin(userId)` |
| Request | Need data back | `GetUserProfile() → Profile` |
| Command | Trigger action | `PlayContent(contentId)` |

### Platform Implementations
| Platform | Implementation |
|----------|---------------|
| iOS/tvOS | Combine, async/await |
| Android | Kotlin Flow, LiveData |
| Web | RxJS, custom EventEmitter |
| Roku | Message ports, task nodes |
| Lightning | Application events |

---

## Domain Question Banks

### OTT / Streaming
- Content types (VOD, Live, Events)
- DRM requirements
- Ad strategy (SSAI/CSAI)
- Offline support
- CDN provider

### E-Commerce
- Catalog size
- Payment providers
- Inventory sync
- Cart persistence

### Extensible
New domains can be added following the template pattern.

---

## Implementation Roadmap

### Phase 1: Setup
- Create command files
- Set up directory structure

### Phase 2: Core Command
- Implement `/create-architecture`
- Confidence tracking
- Research agent prompts

### Phase 3: Output Generation
- Architecture writer
- File templates
- Compliance validation

### Phase 4: Extension
- Implement `/create-service-manager`
- Integration with existing architecture

### Phase 5: Testing & Refinement
- Test across platforms
- Refine question banks
- Update research sources

---

## Files

- `/Commands/create-architecture.md` — Main command
- `/Commands/create-service-manager.md` — Extension command
- `/Architecture/` — Generated output directory
