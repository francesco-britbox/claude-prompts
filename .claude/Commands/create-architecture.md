---
name: create-architecture
description: Generate comprehensive, AI-friendly architecture documentation for any platform
arguments:
  - name: platform
    description: "Target platform: ios, android, web, roku, lightning, firetv-vega, comcast"
    required: true
  - name: description
    description: Optional app description to skip initial question
    required: false
---

# Create Architecture Command

## Overview

You are an expert software architect specializing in multi-platform application development. Generate comprehensive, AI-friendly architecture documentation through an interactive session with confidence tracking.

## Supported Platforms

| Platform | Targets | Tech Stack |
|----------|---------|------------|
| `ios` | iPhone, iPad, Apple TV, Mac Catalyst | Swift, SwiftUI |
| `android` | Mobile, Android TV | Kotlin, Jetpack Compose |
| `web` | Browser, PWA | Next.js, React, Node.js |
| `roku` | Roku devices | BrightScript, SceneGraph |
| `lightning` | LG WebOS, Samsung Tizen | LightningJS, TypeScript |
| `firetv-vega` | Amazon Fire TV | VEGA framework |
| `comcast` | X1, Flex, Sky | RDK, Lightning |

## Execution Flow

### Step 1: Validate Platform

```
IF platform NOT IN [ios, android, web, roku, lightning, firetv-vega, comcast]:
  Display: "Unsupported platform. Supported: ios, android, web, roku, lightning, firetv-vega, comcast"
  EXIT
```

### Step 2: Initialize Research Agent

Immediately spawn a background sub-agent using the Task tool:

```
Spawn: platform-research-agent (background)
Type: general-purpose or Explore
Prompt: |
  Research official documentation and best practices for ${platform} development:

  1. Official vendor documentation and guidelines
  2. Recommended architecture patterns (2024-2026)
  3. State management best practices
  4. Dependency injection patterns
  5. App store / certification requirements
  6. Platform-specific constraints and capabilities

  Focus on authoritative sources. Return structured findings with source URLs.
```

### Step 3: Initialize Confidence Tracker

Maintain these categories throughout the session:

```yaml
confidence_categories:
  platform_targets:
    name: "Platform & Targets"
    weight: 0.15
    minimum: 0.90
    current: 0.0
    notes: ""

  app_type:
    name: "App Type & Domain"
    weight: 0.20
    minimum: 0.85
    current: 0.0
    notes: ""

  content_strategy:
    name: "Content/Data Strategy"
    weight: 0.15
    minimum: 0.75
    current: 0.0
    notes: ""

  features_scope:
    name: "Features & Scope"
    weight: 0.20
    minimum: 0.80
    current: 0.0
    notes: ""

  technical:
    name: "Technical Constraints"
    weight: 0.15
    minimum: 0.70
    current: 0.0
    notes: ""

  integrations:
    name: "Integration & APIs"
    weight: 0.10
    minimum: 0.60
    current: 0.0
    notes: ""

  non_functional:
    name: "Non-Functional"
    weight: 0.05
    minimum: 0.50
    current: 0.0
    notes: ""
```

### Step 4: Interactive Session

Begin conversational questioning. After EACH user response:

1. Update relevant confidence percentages
2. Display understanding summary
3. Ask next highest-value question

**Display Format:**

```
┌─ Understanding ──────────────────────────────────────────────┐
│ Platform & Targets        [XX%]  ✓/○ Status notes           │
│ App Type & Domain         [XX%]  ✓/○ Status notes           │
│ Content/Data Strategy     [XX%]  ✓/○ Status notes           │
│ Features & Scope          [XX%]  ✓/○ Status notes           │
│ Technical Constraints     [XX%]  ✓/○ Status notes           │
│ Integration & APIs        [XX%]  ✓/○ Status notes           │
│ Non-Functional            [XX%]  ✓/○ Status notes           │
├──────────────────────────────────────────────────────────────┤
│ Overall: XX% — [Ready to generate / Need more info]         │
│ [▓▓▓▓▓▓▓▓░░░░░░░░░░░░] Ready at 95%                         │
└──────────────────────────────────────────────────────────────┘
```

**Status Indicators:**
- `✓` = Minimum confidence met
- `◐` = Partial understanding
- `○` = Not yet addressed

### Step 5: Domain Detection & Question Banks

When app description reaches 70% confidence:

1. Classify app type from description
2. Confirm with user: "This sounds like an [OTT/E-commerce/Social/...] app. Correct?"
3. Unlock domain-specific question bank
4. Spawn domain research agent

**Domain Question Banks:**

#### OTT / Streaming
```yaml
questions:
  - "What content types? (VOD, Live TV, Events, Short-form)"
  - "DRM requirements? (FairPlay, Widevine, PlayReady, Multi-DRM)"
  - "Ad strategy? (SSAI, CSAI, Hybrid, None)"
  - "Offline downloads needed?"
  - "CDN provider? (Akamai, Cloudflare, Fastly, CloudFront)"
```

#### E-Commerce
```yaml
questions:
  - "Catalog size? (Small <1K, Medium 1-50K, Large 50K+)"
  - "Payment methods? (Cards, Apple/Google Pay, PayPal, BNPL)"
  - "Inventory sync strategy? (Real-time, Near real-time, On-demand)"
  - "Cart persistence? (Local, Server-synced, Hybrid)"
```

#### Custom Domain
If no predefined domain matches, adapt questions based on user description.

### Step 6: Research Assessment

When platform + domain confidence ≥ 80%:

1. Check research agent results
2. Assess coverage:

```
IF research_coverage < 80% for any critical area:
  Display: "I found limited info on [X]. Deep-diving for more details..."
  Spawn: deep-research-agent for specific topics

ELSE:
  Display: "Research complete. I have sufficient information, but can deep-dive on specific areas if needed."
```

3. Spawn best-practices research agent:

```
Spawn: best-practices-agent
Prompt: |
  Research best practices for ${platform} + ${domain} applications:

  1. SOLID principles application
  2. Dependency injection patterns for ${platform}
  3. State management solutions
  4. Inter-module communication patterns
  5. Error handling strategies
  6. Testing approaches

  Sources: 2024-2026 only. Prefer official docs, vendor blogs, reputable tech blogs.
```

### Step 7: Generation Trigger

**Auto-trigger when:**
- Overall confidence ≥ 95%
- AND all category minimums met

**Manual triggers:**
- User types `done` — Generate at current confidence
- User types `cancel` — Abort session

**Before generation:**
```
Display final summary with all gathered requirements.
Ask: "Ready to generate architecture? (yes/done to proceed, or continue asking questions)"
```

### Step 8: Architecture Generation

Spawn architecture-writer-agent with full context:

```
Spawn: architecture-writer-agent
Prompt: |
  Generate comprehensive architecture documentation.

  ## Context
  Platform: ${platform}
  App Type: ${app_type}
  Requirements: ${all_gathered_requirements}
  Research Findings: ${research_results}

  ## Output Path
  /Users/francesco.farruggia/Documents/Projects/Frameworks/Claude/Architecture/${platform}/${app_type_slug}/

  ## Structure Requirements
  Generate these files in order:

  1. MANIFEST.yaml — Compliance rules, reading order, implementation rules
  2. INDEX.md — Master index with navigation
  3. 01-foundation/ — principles.md, patterns.md, glossary.md
  4. 02-core-services/ — All core service manager specs
  5. 03-domain-services/ — Domain-specific managers based on app type
  6. 04-data-layer/ — Models, transformers, persistence, migration
  7. 05-ui-layer/ — Template system, components, navigation
  8. 06-implementation/ — Setup guide, templates, examples
  9. 07-testing/ — Strategy, patterns, CI integration
  10. 08-appendix/ — Platform specifics, certification, references

  ## Quality Rules
  - 200-400 lines per file maximum
  - Clear cross-references between files
  - Platform-specific implementations
  - Modern patterns only (2024-2026)
  - AI-friendly structure with clear sections

  ## Compliance
  MANIFEST.yaml must enforce:
  - Required reading order with verification questions
  - Implementation rules with severity levels
  - Conditional reading based on task type
```

### Step 9: Validation

After generation, spawn compliance-validator-agent:

```
Spawn: compliance-validator-agent
Prompt: |
  Validate the generated architecture at ${output_path}:

  1. MANIFEST.yaml is valid YAML and contains all required sections
  2. All files referenced in MANIFEST.yaml exist
  3. All INDEX.md files have correct links
  4. Cross-references between files are valid
  5. No file exceeds 400 lines
  6. Each section has proper frontmatter

  Report any issues found.
```

### Step 10: Completion

Display summary:

```
┌─ Architecture Generated ─────────────────────────────────────┐
│                                                              │
│ Location: Architecture/${platform}/${app_type}/              │
│ Files: XX files across 8 sections                            │
│                                                              │
│ Quick Start:                                                 │
│ 1. Read MANIFEST.yaml for compliance rules                   │
│ 2. Start with INDEX.md for navigation                        │
│ 3. Read 01-foundation/ before implementation                 │
│                                                              │
│ Next Steps:                                                  │
│ • Use /create-service-manager to add new services            │
│ • Review 06-implementation/setup-guide.md to begin           │
│                                                              │
└──────────────────────────────────────────────────────────────┘

Want me to deep-dive on any specific area?
```

---

## Core Service Managers (Always Generated)

| Service | Responsibility |
|---------|---------------|
| OrchestrationManager | App flow, bootstrap, action coordination |
| TemplateManager | UI composition, layout rendering |
| EventBusManager | Inter-module pub/sub communication |
| StateManager | Single source of truth, state mutations |
| NetworkManager | API calls, retry logic, request queuing |
| DataManager | Persistence, caching, transformations |
| ErrorManager | Error handling, degradation, messaging |
| TelemetryManager | Instrumentation, event tracking |

---

## Platform-Specific Research Focus

### iOS / tvOS / macOS
- Swift Concurrency patterns
- SwiftUI vs UIKit decisions
- Combine for reactive patterns
- SwiftData / Core Data / SQLite
- App Store Review Guidelines

### Android / Android TV
- Kotlin Coroutines and Flow
- Jetpack Compose patterns
- Hilt / Koin for DI
- Room for persistence
- Play Store policies

### Web (Next.js / React)
- Server Components vs Client Components
- State management (Zustand, Redux, Context)
- API routes and middleware
- IndexedDB for offline

### Roku
- SceneGraph best practices
- Task node patterns
- Memory management
- Certification requirements

### Lightning (LG / Samsung)
- LightningJS component patterns
- Memory optimization for TVs
- Remote control navigation
- Platform-specific APIs

### Fire TV VEGA
- VEGA framework patterns
- Amazon certification
- Alexa integration

### Comcast (X1 / Flex / Sky)
- RDK architecture
- Certification requirements
- Platform constraints

---

## Output Quality Standards

1. **Token Efficiency** — Split large topics, 200-400 lines per file
2. **AI Compliance** — MANIFEST.yaml enforces reading order
3. **Platform Specific** — Implementations match platform best practices
4. **Modern Only** — Patterns from 2024-2026
5. **Cross-Referenced** — Clear links between related files
6. **Actionable** — Implementation guides with examples
