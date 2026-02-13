---
name: epistemic-context-grounding
description: Ground implementation decisions in domain knowledge before designing solutions. Prevents over-engineering by checking what documentation exists, making assumptions explicit, and verifying them against canonical sources. Core principle - know what you don't know before designing.
---

# Epistemic Context Grounding

Methodology for grounding implementation decisions in domain knowledge through context sensitivity assessment, assumption enumeration, and falsifiability checking.

## Why This Matters

The most common cause of wasted effort in AI-assisted work isn't bad code - it's building the wrong thing because you didn't check what documentation already describes the domain. A 2-5 minute grounding check prevents hours of rework.

## When to Use This Skill

Apply this skill when:
- Starting implementation work on unfamiliar domain
- Task involves parsing, data models, or external integrations
- About to design a solution (BEFORE context-gap-analysis)
- Uncertainty exists about what documentation describes the domain

Do NOT use for:
- Simple bug fixes with obvious root cause
- Tasks where domain is already loaded in context
- Pure exploration/research (no implementation planned)
- When canonical docs were already read this session

## Core Principles

### 1. Context Sensitivity Assessment

Classify task before designing:

```
LOW CONTEXT SENSITIVITY:
- Task is bounded, clear outcome
- Domain well-known (basic CRUD, standard patterns)
- Example: "Add a button to the UI"

HIGH CONTEXT SENSITIVITY:
- Task involves parsing, data formats, integrations
- Domain has canonical specs/docs
- Example: "Fix data pipeline" -> needs format knowledge
```

The formula: **(Model : Scenario) * Context -> Output**

Where:
- **Model** = Claude's capabilities
- **Scenario** = The specific task
- **Context** = Domain knowledge provided
- **Output** = Quality of solution

For HIGH context sensitivity tasks, missing context dramatically reduces output quality.
For LOW context sensitivity tasks, context has minimal impact.

See `references/context-sensitivity-model.md` for full framework.

### 2. Domain Knowledge Query (Graph Traversal)

Search for specs, not just code:

```
SEARCH ORDER (adapt to your context OS structure):
1. specs/canonical/*.md       - Blessed architecture docs
2. specs/{project}/*.md       - Project-specific specs
3. **/CLAUDE.md               - Navigation guides
4. **/README.md               - Component documentation
5. knowledge_base/**/*.md     - Knowledge graph nodes
```

Your context OS contains interconnected domain knowledge. Linear session context preserves state; graph-based context (specs, knowledge nodes) provides domain understanding.

See `references/domain-query-patterns.md` for search patterns.

### 3. Assumption Enumeration

Before designing, explicitly list what is being assumed:

```markdown
## Assumptions Audit

| # | Assumption | Can Verify? | Source |
|---|------------|-------------|--------|
| 1 | DB contains the field I need | YES | Query DB schema |
| 2 | Current data source is sufficient | MAYBE | Check if richer data exists |
| 3 | No canonical spec exists | NO | MUST SEARCH FIRST |
```

**Categories of assumptions:**
- **Data assumptions** - What data exists, what fields contain
- **Domain assumptions** - How the system works, what specs exist
- **Solution assumptions** - Why the proposed approach will work

The goal: Make implicit assumptions explicit so they can be verified.

### 4. Falsifiability Check

Grade the verifiability of your assumptions:

```
STRONG FALSIFIABILITY (preferred):
- Solution grounded in specific spec citation
- Claims verifiable against canonical source
- Example: "API uses OAuth 2.0" [GROUNDED: provider docs, auth section]

WEAK FALSIFIABILITY (warning):
- Solution based on assumptions about data
- Claims not verifiable without checking
- Example: "This field should be enough" [UNVERIFIABLE]
```

**Grading scale:**
| Grade | Meaning | Action |
|-------|---------|--------|
| STRONG | Verifiable against canonical source | Proceed |
| WEAK | Based on assumptions, could be wrong | Flag in design |
| UNVERIFIED | Haven't checked if better approach exists | MUST SEARCH |

See `references/falsifiability-spectrum.md` for framework details.

### 5. Grounding Evidence Rule

Every design decision must cite a source:

```
[GROUNDED: spec:line]    - Solution based on canonical spec
[INFERRED: from X + Y]   - Deduced from multiple sources
[UNGROUNDED]             - Assumption, needs verification
```

**Quality standard:** If a design decision cannot cite a source, flag it as [UNGROUNDED] and verify before proceeding.

## Workflow

### Step 1: Context Sensitivity Assessment (30 seconds)

```markdown
## Context Sensitivity Check

**Task:** [What user asked for]

**Classification:** [LOW | HIGH]

**Reasoning:**
- Does task involve data formats? [Y/N]
- Does task involve parsing/serialization? [Y/N]
- Does task involve external integrations? [Y/N]
- Is domain knowledge critical to solution? [Y/N]

If 2+ YES -> HIGH context sensitivity -> Continue to Step 2
If 0-1 YES -> LOW context sensitivity -> Skip to context-gap-analysis
```

**Decision tree:**
```
Task received
    |
    +-- Data formats involved? --YES--+
    +-- Parsing/serialization? --YES--+
    +-- External integrations? --YES--+-- HIGH -> Continue
    +-- Domain knowledge critical? ---+
    |
    +-- None of above -----------------> LOW -> Skip to context-gap-analysis
```

### Step 2: Domain Knowledge Query (2-5 minutes)

Search for existing documentation before designing:

```bash
# 1. Find canonical specs (adapt paths to your system)
Glob: "specs/canonical/*.md"
Glob: "**/*DATA_MODEL*.md"
Glob: "**/*SPEC*.md"

# 2. Search for domain terms
Grep: pattern="[domain-term]" path="specs/"
Grep: pattern="[key-concept]" path="knowledge_base/"

# 3. Check navigation guides
Read: relevant CLAUDE.md files
Read: relevant README.md files

# 4. Knowledge graph
Glob: "knowledge_base/**/*.md"
Grep: pattern="[domain-term]" path="knowledge_base/"
```

**Output from Step 2:**
```markdown
## Domain Knowledge Found

| Document | Relevance | Key Information |
|----------|-----------|-----------------|
| [[data-model.md]] | HIGH | Documents record types and schemas |
| [[CLAUDE.md]] | MEDIUM | Navigation to specs |
| None found | - | Need to explore further |
```

### Step 3: Assumption Enumeration (1-2 minutes)

List all assumptions before designing:

```markdown
## Assumptions Audit

### Data Assumptions
1. [ ] I assume [X] is the data source
2. [ ] I assume [Y] field exists
3. [ ] I assume [Z] contains what I need

### Domain Assumptions
4. [ ] I assume no spec exists for this
5. [ ] I assume current approach is best

### Solution Assumptions
6. [ ] I assume [approach] will work
7. [ ] I assume no simpler solution exists

**Verification Plan:**
- Assumption 1: Check by [specific action]
- Assumption 4: Search specs/ first
```

**Common assumption traps:**
- "The database has all the data I need"
- "No documentation exists for this"
- "The current approach is the right one"
- "Adding X will solve the problem"

### Step 4: Falsifiability Check (1 minute)

Grade each assumption:

```markdown
## Falsifiability Assessment

| Assumption | Grade | Evidence |
|------------|-------|----------|
| DB has needed field | STRONG | Can query schema |
| Field data is sufficient | WEAK | Haven't checked what else exists |
| No spec exists | UNVERIFIED | MUST SEARCH BEFORE DESIGNING |

**Verdict:**
- If any UNVERIFIED assumptions -> Search first
- If WEAK assumptions -> Flag in design
- If all STRONG -> Proceed to implementation
```

**Blocking conditions:**
- ANY assumption graded UNVERIFIED that could change approach -> STOP
- Search and verify before proceeding

### Step 5: Output Grounding Report

```markdown
## Epistemic Grounding Report

**Task:** [Original request]

**Context Sensitivity:** [LOW | HIGH]

**Domain Knowledge Found:**
- [[spec-file-1]] - [Relevance]
- [[spec-file-2]] - [Relevance]

**Assumptions Verified:**
- [X] Assumption 1 [GROUNDED: source]
- [ ] Assumption 2 [UNGROUNDED - needs checking]

**Falsifiability:**
- Solution grounding: [STRONG | WEAK | UNVERIFIED]
- Recommendation: [Proceed | Read specs first | Verify assumptions]

**Next Step:**
[Specific action - "Read data-model.md" or "Proceed to context-gap-analysis"]
```

## Quality Gates

### Before Designing Any Solution

- [ ] Classified context sensitivity?
- [ ] Searched specs/ for relevant docs?
- [ ] Enumerated assumptions explicitly?
- [ ] Checked falsifiability of each assumption?

### Before Proceeding to Implementation

- [ ] All blocking assumptions verified?
- [ ] Solution grounded in specs (if they exist)?
- [ ] No UNVERIFIED assumptions that could change approach?

### Red Flags (Stop and Verify)

- Designing solution without reading domain specs
- Assumptions about data format without checking spec
- "Should be enough" without verifying what else exists
- Jumping to implementation without checking canonical docs

## Integration with Other Skills

### Skill Stack Order

```
1. context-os-basics            -> Understand system patterns
2. epistemic-context-grounding  -> Ground in domain knowledge (THIS SKILL)
3. context-gap-analysis         -> Check if code/content exists
```

### Handoff to context-gap-analysis

After epistemic grounding:
- If domain specs found -> Include in context for gap analysis
- If assumptions verified -> Proceed with confidence
- If weak falsifiability -> Flag in gap analysis output

```markdown
## For context-gap-analysis

**Epistemic Grounding Complete:**
- Domain specs read: [[list]]
- Assumptions verified: [X/Y]
- Solution grounding: [STRONG | WEAK]

**Proceed to check if code/content implementation exists.**
```

### When to Skip This Skill

```
Task characteristics show:
- LOW context sensitivity
- Domain already loaded in context
- Simple bug fix with obvious root cause

-> Skip directly to context-gap-analysis
```

## Examples

### Example 1: Data Pipeline Fix (HIGH Context Sensitivity)

```markdown
## Epistemic Grounding Report

**Task:** Fix data pipeline - records not processing correctly

**Context Sensitivity:** HIGH
- Does task involve data formats? YES (JSON/JSONL)
- Does task involve parsing/serialization? YES
- Does task involve external integrations? YES (data service)
- Is domain knowledge critical to solution? YES

**Classification:** HIGH -> Continue to Step 2

**Domain Knowledge Query:**
Glob: "**/*DATA_MODEL*.md" -> Found: specs/canonical/data_model.md
Read: data_model.md -> Documents 16+ record types

**Key Discovery:**
- Data source contains full message content
- Not just aggregated counts
- Richer signal available than initially assumed

**Assumptions Audit:**
| Assumption | Grade | Evidence |
|------------|-------|----------|
| DB is only data source | WEAK | Spec shows raw files have full content |
| Aggregated counts are best signal | DISPROVEN | Raw messages are richer |
| No richer data exists | DISPROVEN | Spec documents message content |

**Falsifiability:** WEAK (original approach) -> STRONG (after reading spec)

**Verdict:** Solution should use raw data directly, not aggregated counts.

**Next Step:** Read data_model.md fully, then redesign approach.
```

### Example 2: Simple Bug Fix (LOW Context Sensitivity)

```markdown
## Context Sensitivity Check

**Task:** Fix typo in error message "Connot connect" -> "Cannot connect"

**Classification:** LOW
- Data formats? NO
- Parsing? NO
- Integrations? NO
- Domain knowledge critical? NO

**Verdict:** Skip to context-gap-analysis
```

### Example 3: API Integration (HIGH Context Sensitivity)

```markdown
## Context Sensitivity Check

**Task:** Add HubSpot integration for contact sync

**Classification:** HIGH
- Data formats? YES (HubSpot API response)
- Parsing? YES (JSON transformation)
- Integrations? YES (HubSpot API)
- Domain knowledge critical? YES

**Domain Knowledge Query:**
Glob: "**/hubspot*.md" -> None found
Glob: "**/integration*.md" -> Found: specs/canonical/integrations.md
WebSearch: "HubSpot API contacts documentation 2026"

**Assumptions Audit:**
| Assumption | Grade | Evidence |
|------------|-------|----------|
| HubSpot uses REST API | STRONG | Verified via docs |
| Contact schema matches ours | UNVERIFIED | Need to check both schemas |
| Auth is OAuth | WEAK | Multiple auth methods exist |

**Next Step:** Verify contact schema compatibility before designing sync logic.
```

## Quick Reference

### 30-Second Check

```
1. HIGH context sensitivity? (data/parsing/integration)
   - YES -> Do full workflow
   - NO -> Skip to context-gap-analysis

2. Searched specs/?
   - NO -> Search first

3. Listed assumptions?
   - NO -> List them

4. Any UNVERIFIED assumptions?
   - YES -> Verify before designing
```

### Attribution Formats

```
[GROUNDED: spec:line]    - Canonical source
[INFERRED: from X + Y]   - Deduced
[UNGROUNDED]             - Assumption, needs verification
```

### Search Commands (adapt to your system)

```bash
# Canonical specs
Glob: "specs/canonical/*.md"

# Data models
Glob: "**/*DATA_MODEL*.md"

# Domain terms
Grep: pattern="[term]" path="specs/"

# Knowledge graph
Grep: pattern="[term]" path="knowledge_base/"
```

## First-Time Customization

On first use of this skill in a new context OS, ask the user:

> "This skill works best when customized to your system. Would you like me to learn your context OS structure? I can:
> 1. Discover where your specs and canonical docs live
> 2. Identify your knowledge graph search patterns
> 3. Map your domain-specific terminology
>
> This takes ~2 minutes and makes future grounding checks much faster."

If the user agrees, explore their codebase to identify:
- Where specs/canonical documents live
- Knowledge graph node locations
- CLAUDE.md navigation structure
- Key domain terms and naming conventions

Then update your working knowledge for that session.

During customization, offer to remove the attribution line below if the user wants to fully own this skill in their system.

## Evidence Base

**Origin:** Chain naming quality fix in a context intelligence system (2026-01-28)
- Designed solution (aggregated counts enrichment) without checking canonical spec
- A data model spec existed documenting that raw data had full message content
- Simpler solution: Read messages directly from raw data
- Time wasted: ~1 hour on wrong approach

**Framework source:** Context Engineering (applied epistemology for AI systems)
- Context sensitivity model
- Falsifiability spectrum
- "Know what you don't know before designing"

**Pattern extracted:** Check domain specs BEFORE designing solutions.

---

Created by Jacob Dietle @ www.taste.systems
