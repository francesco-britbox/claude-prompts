---
name: create-service-manager
description: Create a new service manager conforming to an existing architecture
arguments:
  - name: name
    description: "Service manager name (e.g., RecommendationManager)"
    required: true
  - name: architecture
    description: "Path to architecture directory (auto-detected if omitted)"
    required: false
---

# Create Service Manager Command

## Overview

Create a new service manager that fully conforms to an existing architecture's patterns, compliance rules, and conventions. The generated service manager will integrate seamlessly with the existing architecture.

## Execution Flow

### Step 1: Locate Architecture

```
IF --architecture provided:
  architecture_path = provided path
ELSE:
  Search for Architecture/ directory in:
    1. Current working directory
    2. Parent directories (up to 3 levels)

  IF multiple architectures found:
    List options and ask user to select

  IF no architecture found:
    Display: "No architecture found. Run /create-architecture first or specify --architecture path"
    EXIT
```

Validate architecture:
```
IF NOT exists(architecture_path/MANIFEST.yaml):
  Display: "Invalid architecture: MANIFEST.yaml not found at ${architecture_path}"
  EXIT
```

### Step 2: Load Architecture Context

Read and parse these files:

```yaml
required_files:
  - MANIFEST.yaml              # Compliance rules
  - 02-core-services/INDEX.md  # Existing services
  - 02-core-services/service-manager-base.md  # Base contract
  - 02-core-services/event-bus.md             # Communication patterns

optional_files:
  - 03-domain-services/INDEX.md  # Existing domain services
  - 03-domain-services/*.md      # Domain service examples
```

Extract:
- Platform and app type
- Existing service managers
- Event bus patterns (events, requests, commands)
- Base service manager contract
- Compliance rules

### Step 3: Validate Service Name

```
IF ${name} already exists in architecture:
  Display: "${name} already exists. Choose a different name or update the existing service."
  EXIT

IF ${name} does not end with "Manager":
  Suggest: "${name}Manager" as the name
  Confirm with user
```

### Step 4: Interactive Questions

Ask focused questions to understand the new service:

**Q1: Single Responsibility**
```
"What is ${name}'s single responsibility?"

Requirements:
- Must be ONE clear sentence
- Must not overlap with existing services
- Must follow Single Responsibility Principle

Example good answers:
- "Manage personalized content recommendations based on user behavior"
- "Handle in-app purchase transactions and receipt validation"
- "Coordinate background download tasks and storage management"
```

**Q2: Service Communication**
```
"Which existing services will ${name} communicate with?"

Display list of existing services:
□ OrchestrationManager
□ NetworkManager
□ DataManager
□ StateManager
□ TelemetryManager
□ ErrorManager
□ [domain-specific services...]

Allow multi-select
```

**Q3: Event Subscriptions**
```
"What events will ${name} subscribe to?"

Based on selected services, show their published events:
- AuthManager publishes: UserDidLogin, UserDidLogout, SessionExpired
- PlayerManager publishes: PlaybackStarted, PlaybackCompleted, PlaybackError
- [etc.]

Allow selection + custom event definition
```

**Q4: Event Publications**
```
"What events will ${name} publish?"

For each event:
- Event name (PascalCase)
- Payload structure
- Expected consumers
```

**Q5: Persistence Requirements**
```
"Does ${name} need to persist data?"

IF yes:
  - "What data needs persistence?"
  - "Cache only or permanent storage?"
  - "Should it survive app reinstall?"
```

**Q6: External Dependencies**
```
"Does ${name} require external SDKs or APIs?"

IF yes:
  - "Which SDKs/APIs?"
  - "Are they platform-specific?"
```

### Step 5: Generate Service Manager Specification

Create file: `03-domain-services/${name-kebab-case}.md`

**File Template:**

```markdown
---
id: "03-domain-services/${name-kebab-case}"
created: "${current_date}"
requires:
  - "02-core-services/service-manager-base.md"
  - "02-core-services/event-bus.md"
  ${additional_requires}
rules_enforced:
  - "SVC-001"
  - "SVC-002"
  ${additional_rules}
---

# ${name}

## Responsibility

${single_responsibility_statement}

## Dependencies

| Service | Purpose |
|---------|---------|
${dependency_table}

## Events

### Subscribes To

| Event | Source | Handler Action |
|-------|--------|----------------|
${subscription_table}

### Publishes

| Event | Payload | Consumers |
|-------|---------|-----------|
${publication_table}

## Data Model

${data_model_if_applicable}

## Persistence

${persistence_strategy_if_applicable}

## External Dependencies

${external_deps_if_applicable}

## Lifecycle

### Initialization
${init_requirements}

### Runtime
${runtime_behavior}

### Cleanup
${cleanup_requirements}

## Implementation Notes

### ${platform} Specific

${platform_specific_guidance}

## Testing

### Unit Tests
- Test ${name} in isolation with mocked dependencies
- Verify event handling for each subscription
- Verify event emission with correct payloads

### Integration Tests
- Test communication with dependent services
- Verify correct registration with EventBus

## Example Usage

\`\`\`${code_language}
// Example: Subscribing to ${name} events
${example_code}
\`\`\`
```

### Step 6: Update Architecture Files

**Update MANIFEST.yaml:**
```yaml
# Add to service registry
services:
  domain:
    - name: ${name}
      path: "03-domain-services/${name-kebab-case}.md"
      depends_on: [${dependencies}]

# Add any new rules if needed
implementation_rules:
  - id: "${rule_id}"
    rule: "${new_rule_if_applicable}"
    severity: "medium"
```

**Update 03-domain-services/INDEX.md:**
```markdown
## Domain Services

| Service | Responsibility | Added |
|---------|---------------|-------|
...existing...
| [${name}](${name-kebab-case}.md) | ${responsibility} | ${date} |
```

**Update related service files:**
If the new service publishes events that existing services might consume, add cross-references.

### Step 7: Validation

Validate the generated service:

```
Checks:
□ File created at correct location
□ YAML frontmatter is valid
□ All referenced services exist
□ No circular dependencies introduced
□ Event names follow conventions
□ Conforms to base service manager contract
```

### Step 8: Completion

Display summary:

```
┌─ Service Manager Created ────────────────────────────────────┐
│                                                              │
│ Created: 03-domain-services/${name-kebab-case}.md            │
│                                                              │
│ Updated:                                                     │
│ • MANIFEST.yaml (service registry)                           │
│ • 03-domain-services/INDEX.md (navigation)                   │
│                                                              │
│ ${name} is ready for implementation.                         │
│                                                              │
│ Next Steps:                                                  │
│ 1. Review the generated specification                        │
│ 2. Implement ${name} following the contract                  │
│ 3. Register with ServiceRegistry in your app                 │
│ 4. Write unit tests per testing section                      │
│                                                              │
│ Compliance:                                                  │
│ • Must extend BaseServiceManager                             │
│ • Must communicate only via EventBus                         │
│ • Must emit telemetry for key operations                     │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## Service Manager Contract

Every generated service manager must satisfy:

```yaml
base_contract:
  lifecycle_methods:
    - initialize()    # Called once at registration
    - start()         # Called when app ready
    - pause()         # Called on background
    - resume()        # Called on foreground
    - dispose()       # Called on shutdown

  required_dependencies:
    - eventBus        # For communication
    - telemetry       # For instrumentation
    - errorHandler    # For escalation

  required_methods:
    - getName(): string
    - getDependencies(): ServiceManager[]
    - onEvent(event): void

  communication_rules:
    - MUST use EventBus for inter-service communication
    - MUST NOT directly reference other service managers
    - MUST NOT hold references to UI components
    - MUST emit telemetry for public method calls
```

---

## Event Naming Conventions

```yaml
events:
  pattern: "${Subject}Did${Action}" or "${Subject}Will${Action}"
  examples:
    - UserDidLogin
    - ContentWillPlay
    - DownloadDidComplete
    - SessionDidExpire

requests:
  pattern: "Get${Resource}" or "Fetch${Resource}"
  examples:
    - GetUserProfile
    - FetchRecommendations

commands:
  pattern: "${Action}${Subject}"
  examples:
    - PlayContent
    - PauseDownload
    - RefreshToken
```

---

## Platform-Specific Implementation Patterns

### iOS / tvOS / macOS (Swift)
```swift
final class ${name}: BaseServiceManager {
    private let eventBus: EventBus
    private let telemetry: TelemetryManager

    init(eventBus: EventBus, telemetry: TelemetryManager) {
        self.eventBus = eventBus
        self.telemetry = telemetry
    }

    func start() async {
        await eventBus.subscribe(to: SomeEvent.self, handler: handleSomeEvent)
    }
}
```

### Android (Kotlin)
```kotlin
class ${name} @Inject constructor(
    private val eventBus: EventBus,
    private val telemetry: TelemetryManager
) : BaseServiceManager() {

    override fun start() {
        eventBus.subscribe<SomeEvent> { event ->
            handleSomeEvent(event)
        }
    }
}
```

### Web (TypeScript)
```typescript
export class ${name} extends BaseServiceManager {
    constructor(
        private eventBus: EventBus,
        private telemetry: TelemetryManager
    ) {
        super();
    }

    start(): void {
        this.eventBus.subscribe(SomeEvent, this.handleSomeEvent.bind(this));
    }
}
```

### Roku (BrightScript)
```brightscript
function ${name}_init()
    m.eventBus = m.top.findNode("eventBus")
    m.telemetry = m.top.findNode("telemetryManager")
end function

function ${name}_start()
    m.eventBus.observeField("someEvent", "${name}_handleSomeEvent")
end function
```

### Lightning (JavaScript)
```javascript
export class ${name} extends BaseServiceManager {
    static _template() {
        return {};
    }

    _init() {
        this.eventBus = this.application.eventBus;
        this.telemetry = this.application.telemetry;
    }

    _active() {
        this.eventBus.on('someEvent', this._handleSomeEvent.bind(this));
    }
}
```
