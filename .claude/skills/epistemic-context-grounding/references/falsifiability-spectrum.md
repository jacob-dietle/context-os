# Falsifiability Spectrum

Framework for grading the verifiability of assumptions and design decisions, preventing solutions built on weak foundations.

## Core Concept

**Falsifiability** = The degree to which a claim can be tested against evidence and potentially proven wrong.

In software engineering context:
- **STRONG falsifiability** = Claim verifiable against canonical source (spec, doc, schema)
- **WEAK falsifiability** = Claim based on assumptions, could be wrong
- **UNVERIFIED** = Haven't checked whether claim is true or better approach exists

## The Spectrum

```
STRONG ---------------------- WEAK ---------------------- UNVERIFIED
   |                          |                             |
   v                          v                             v
Verifiable              Assumptions                    Haven't
against source          could be wrong                 checked
   |                          |                             |
   v                          v                             v
PROCEED                 FLAG IN DESIGN               STOP & VERIFY
```

## Grading Scale

### STRONG Falsifiability

**Definition:** Claim can be directly verified against a canonical source.

**Characteristics:**
- Cites specific spec, doc, or schema
- Can be tested by reading the source
- If wrong, the source would clearly contradict

**Attribution format:**
```
[GROUNDED: spec:line] - Solution based on canonical spec
```

**Examples:**
```
 "API uses OAuth 2.0" [GROUNDED: provider docs, auth section]
 "Database table has created_at column" [GROUNDED: schema.sql:45]
 "Webhook payload includes event_type" [GROUNDED: integration spec:78]
```

**Action:** Proceed with confidence.

### WEAK Falsifiability

**Definition:** Claim based on assumptions that could be wrong but aren't blocking.

**Characteristics:**
- Based on inference or pattern matching
- Source doesn't directly confirm
- Alternative approaches may exist

**Attribution format:**
```
[INFERRED: from X + Y] - Deduced from multiple sources
```

**Examples:**
```
~ "Aggregated counts should provide enough signal"
~ "This pattern should work based on similar code"
~ "Adding this field will improve quality"
```

**Action:** Flag in design, acknowledge uncertainty.

### UNVERIFIED

**Definition:** Haven't checked whether claim is true or better approach exists.

**Characteristics:**
- Assumption about data/format without checking spec
- Haven't searched for canonical documentation
- Solution designed without verifying foundation

**Attribution format:**
```
[UNGROUNDED] - Assumption, needs verification
```

**Examples:**
```
x "No spec exists for this" [Haven't searched]
x "Database is the only data source" [Haven't checked alternatives]
x "This is the simplest approach" [Haven't enumerated alternatives]
```

**Action:** STOP and verify before proceeding.

## The Falsifiability Test

For each assumption or design decision, ask:

```
1. Can I point to a source that confirms this?
   YES -> STRONG
   NO -> Continue to #2

2. Am I inferring from related information?
   YES -> WEAK (flag it)
   NO -> Continue to #3

3. Have I checked whether this is true?
   YES -> Depends on what I found
   NO -> UNVERIFIED (stop and check)
```

## Blocking vs Non-Blocking

### Blocking UNVERIFIED Assumptions

**Pattern:** Assumptions that, if wrong, would invalidate the entire approach.

**Examples:**
- "Database has all the data I need" (what if raw files have more?)
- "No canonical spec exists" (what if it does?)
- "This is the right approach" (have you checked alternatives?)

**Rule:** MUST verify before designing solution.

### Non-Blocking WEAK Assumptions

**Pattern:** Assumptions that, if wrong, would require minor adjustments.

**Examples:**
- "This naming convention is best" (can change later)
- "Error message should be phrased this way" (style choice)
- "Function should return X type" (can refactor)

**Rule:** Flag in design, proceed with awareness.

## Connection to Hallucination Prevention

The falsifiability spectrum directly addresses hallucination risk:

| Falsifiability | Hallucination Risk | Mitigation |
|----------------|-------------------|------------|
| STRONG | LOW | Grounded in source |
| WEAK | MEDIUM | Flagged as inference |
| UNVERIFIED | HIGH | Must verify first |

**Key insight:** Hallucinations often come from UNVERIFIED assumptions treated as facts.

## Practical Application

### Before Designing

Create falsifiability audit:

```markdown
## Falsifiability Audit

| Decision/Assumption | Grade | Evidence | Action |
|---------------------|-------|----------|--------|
| Data format is documented | ? | Haven't checked | SEARCH |
| Database has needed field | STRONG | Schema query | PROCEED |
| Field data is sufficient | WEAK | No comparison done | FLAG |
| No simpler approach exists | UNVERIFIED | Haven't enumerated | STOP |
```

### During Design

Track grounding for each decision:

```markdown
## Design Decisions

1. Read data from primary source
   - [GROUNDED: verified schema has field]
   - [WEAK: assuming it's sufficient signal]

2. Send to processing service
   - [GROUNDED: service API spec]
   - [UNGROUNDED: haven't checked if richer data exists]
```

### After Design Review

Verify all UNVERIFIED items before implementation:

```markdown
## Pre-Implementation Verification

[ ] Searched specs/ for data format docs
[ ] Verified data source assumptions
[ ] Checked for alternative approaches
[ ] All blocking assumptions now STRONG or WEAK
```

## Evidence-Based Attribution Patterns

### Attribution in Documentation

```markdown
The solution reads messages directly from raw data.
[GROUNDED: data_model.md:120-145 documents message types]

Usage counts are aggregated from sessions.
[INFERRED: pattern from existing query code + schema analysis]

This should improve output quality.
[UNGROUNDED: hypothesis, needs A/B testing to verify]
```

### Attribution in Code Comments

```python
# Read first user message for naming
# [GROUNDED: data_model.md documents message types]
first_message = extract_first_user_message(session)

# Aggregate tool usage - may not be sufficient signal
# [WEAK: assuming counts correlate with work type]
tools_used = aggregate_tool_usage(sessions)
```

### Attribution in Design Docs

```markdown
## Approach

1. **Extract first user message from raw data**
   - [GROUNDED: Spec documents full message content available]
   - Provides strongest signal of user intent

2. **Fall back to aggregated counts**
   - [WEAK: Secondary signal, less direct than message content]
   - Useful when message extraction fails
```

## Common Patterns

### The "It Should Work" Pattern

**Symptom:** Designing based on how things "should" work.

```
x "The API should return this format"
x "The database should have this field"
x "This approach should solve the problem"
```

**Fix:** Replace "should" with evidence:

```
 "The API returns X format" [GROUNDED: API docs section Y]
 "Database has field Z" [GROUNDED: schema.sql:line]
 "Approach solves problem because..." [GROUNDED: similar pattern in file:line]
```

### The "No Spec Exists" Pattern

**Symptom:** Assuming documentation doesn't exist without searching.

```
x "There's no spec for this data format"
x "This isn't documented anywhere"
x "I'll have to figure this out from the code"
```

**Fix:** Search before assuming:

```
Glob: "**/*DATA_MODEL*.md"
Glob: "specs/canonical/*.md"
Grep: pattern="[format name]" path="specs/"

Then:
 "Found data_model.md documenting format"
OR
 "Searched specs/, no format documentation exists" [VERIFIED]
```

### The "Only Option" Pattern

**Symptom:** Assuming current approach is the only viable one.

```
x "We have to use the database"
x "This is the only way to do this"
x "There's no other data source"
```

**Fix:** Enumerate alternatives:

```
## Alternative Approaches

1. Database query (current assumption)
2. Raw file parsing (check if richer data)
3. Index file (check if pre-computed)
4. External service (check if API available)

Then select with evidence:
 "Raw data has richer content than database" [GROUNDED: spec comparison]
```

## Quality Standards

**Healthy design shows:**
- Most decisions GROUNDED in sources
- WEAK assumptions explicitly flagged
- No UNVERIFIED blocking assumptions
- Sources cited inline

**Warning signs:**
- Many "should" statements without sources
- Assumptions treated as facts
- No citations or evidence
- "I assumed..." discovered late

## Integration with Other Concepts

### With Context Sensitivity

HIGH context sensitivity -> More assumptions to verify -> More falsifiability checks needed

### With Assumption Enumeration

Enumerated assumptions feed into falsifiability grading:
1. List assumptions
2. Grade each (STRONG/WEAK/UNVERIFIED)
3. Verify UNVERIFIED before proceeding

### With Domain Knowledge Query

Domain knowledge query resolves UNVERIFIED assumptions:
- Search finds spec -> Assumption becomes STRONG
- Search finds nothing -> Assumption stays WEAK (but now verified as missing)
