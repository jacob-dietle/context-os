# Context Sensitivity Model

Deep dive on context sensitivity assessment - determining when domain knowledge critically affects solution quality.

## The Core Formula

```
(Model : Scenario) * Context -> Output
```

Where:
- **Model** = Claude's capabilities (reasoning, code generation, etc.)
- **Scenario** = The specific task being performed
- **Context** = Domain knowledge, specs, documentation provided
- **Output** = Quality and correctness of solution

**Key insight:** For some tasks, Context has multiplicative effect on Output. For others, it's nearly irrelevant.

## The Multiplication Effect

### LOW Context Sensitivity

Context has minimal multiplicative effect:

```
Model(excellent) * Scenario(simple) * Context(any) -> Output(good)
```

**Characteristics:**
- Task is bounded with clear outcome
- Domain is well-known (standard patterns, common libraries)
- Solution space is small
- Error is easily detected and corrected

**Examples:**
- Fix a typo in an error message
- Add a button to the UI
- Write a simple utility function
- Basic CRUD operations

For these tasks, even minimal context produces good output because the Model capability dominates.

### HIGH Context Sensitivity

Context has dramatic multiplicative effect:

```
Model(excellent) * Scenario(complex) * Context(missing) -> Output(poor)
Model(excellent) * Scenario(complex) * Context(rich) -> Output(excellent)
```

**Characteristics:**
- Task involves data formats, parsing, serialization
- Domain has canonical specs/documentation
- Multiple valid approaches exist
- Error is subtle and hard to detect

**Examples:**
- Parse structured data files with specific record types
- Integrate with external API
- Handle complex data transformations
- Design system architecture

For these tasks, missing context causes dramatic quality degradation even with excellent model capability.

## Decision Tree

```
Task received
    |
    +--- Does task involve data formats?
    |         |
    |         +-- YES -> HIGH (data formats have specs)
    |         +-- NO --+
    |                  |
    +--- Does task involve parsing/serialization?
    |         |       |
    |         +-- YES -> HIGH (format knowledge required)
    |         +-- NO --+
    |                  |
    +--- Does task involve external integrations?
    |         |       |
    |         +-- YES -> HIGH (API specs required)
    |         +-- NO --+
    |                  |
    +--- Is domain knowledge critical to solution?
    |         |       |
    |         +-- YES -> HIGH (domain specs exist)
    |         +-- NO --+
    |                  |
    +--- None of above +-------> LOW
```

**Scoring shortcut:**
- 2+ YES answers -> HIGH context sensitivity
- 0-1 YES answers -> LOW context sensitivity

## Category Examples

### Data Formats (HIGH)

| Task | Why HIGH | Required Context |
|------|----------|------------------|
| Parse JSONL files | Multiple record types, specific schemas | Data model spec |
| Handle CSV exports | Encoding, escaping, column mapping | Format documentation |
| Process XML feeds | Namespaces, schema validation | XSD or DTD |
| Parse log files | Custom formats, timestamps | Log format spec |

### Parsing/Serialization (HIGH)

| Task | Why HIGH | Required Context |
|------|----------|------------------|
| Serialize to protocol buffers | Schema required | .proto files |
| Deserialize API responses | Field mapping, types | API documentation |
| Transform data between formats | Both source and target specs | Format specifications |
| Handle date/time parsing | Timezone, locale rules | Format conventions |

### External Integrations (HIGH)

| Task | Why HIGH | Required Context |
|------|----------|------------------|
| CRM API integration | Auth, endpoints, schemas | API documentation |
| Database migrations | Schema, constraints, indexes | Schema documentation |
| OAuth implementation | Flow, scopes, tokens | Provider documentation |
| Webhook handling | Payload format, validation | Webhook spec |

### Simple Operations (LOW)

| Task | Why LOW | Context Impact |
|------|---------|----------------|
| Fix typo | Self-evident correction | Minimal |
| Add console.log | Debugging, no format | None |
| Rename variable | Refactoring, no logic | Minimal |
| Update dependency | Package management | Minimal |

## The Danger Zone

### Misclassifying HIGH as LOW

**Pattern:** Assuming task is simple, jumping to solution without checking context.

**Example:**
- Appeared simple: "improve naming quality"
- Actually HIGH: involves structured data format, data extraction, prompt engineering
- Missed context: a data model spec documented full message content
- Result: Designed wrong solution (aggregated counts vs. raw messages)

**Warning signs:**
- "This should be straightforward"
- "I know how to do this"
- "Let me just add X"
- Designing without reading specs

### Misclassifying LOW as HIGH

**Pattern:** Over-engineering simple tasks with unnecessary specification.

**Example:**
- Actually simple: Change "Connot" to "Cannot"
- Over-engineering: Searching for i18n specs, localization frameworks
- Result: Wasted time on unnecessary research

**Warning signs:**
- Simple task taking too long
- Reading specs for obvious fixes
- Adding complexity to bounded problems

## Context Sensitivity by Domain

### Codebase-Specific

| Domain | Typical Sensitivity | Why |
|--------|---------------------|-----|
| UI styling | LOW | Standard CSS/patterns |
| Business logic | MEDIUM-HIGH | Domain rules encoded |
| Data layer | HIGH | Schema, format specs |
| Infrastructure | HIGH | Config, deployment specs |

### Technology-Specific

| Technology | Typical Sensitivity | Why |
|------------|---------------------|-----|
| React components | LOW-MEDIUM | Well-documented patterns |
| API design | HIGH | Contract specifications |
| Database queries | MEDIUM-HIGH | Schema knowledge required |
| ML/AI integration | HIGH | Model specs, data formats |

## Practical Application

### Before Starting Any Task

**30-second assessment:**

```markdown
## Quick Context Sensitivity Check

Task: [description]

[ ] Data formats involved?
[ ] Parsing/serialization?
[ ] External integrations?
[ ] Domain knowledge critical?

Score: [0-4] YES answers
Classification: [LOW if 0-1 | HIGH if 2+]
```

### When Uncertain

Default to HIGH and do a quick spec search:

```bash
# 30-second search (adapt paths to your system)
Glob: "specs/canonical/*.md"
Glob: "**/*SPEC*.md"
Glob: "**/*DATA_MODEL*.md"
```

If specs found -> Task is probably HIGH
If no specs -> May be LOW (or specs missing)

## Connection to Other Concepts

### Falsifiability

HIGH context sensitivity correlates with:
- Need for STRONG falsifiability
- More assumptions to enumerate
- Higher risk of UNVERIFIED assumptions

### Domain Knowledge Query

HIGH context sensitivity triggers:
- Full spec search workflow
- Knowledge graph traversal
- Canonical doc discovery

### Assumption Enumeration

HIGH context sensitivity requires:
- Explicit data assumptions
- Format assumptions
- Integration assumptions

## Evidence Base

**Pattern observed:** Tasks with HIGH context sensitivity that are treated as LOW result in:
- Wrong solutions designed
- Rework required
- Time wasted (often 1-2 hours)
- "I didn't know that spec existed" moments

**Counterfactual:** Proper classification prevents these failures by triggering domain knowledge query before design.
