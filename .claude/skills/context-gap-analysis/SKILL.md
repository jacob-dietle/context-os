---
name: context-gap-analysis
description: Enumerate required context, check what exists, and find the simplest path forward before any task. Prevents hallucinations by forcing the agent to verify assumptions before acting.
---

# Context Gap Analysis

Systematic methodology for checking what you need, what exists, and finding the simplest path forward.

**Core Principle:** "What if it's already done? Check before you build."

## Why This Matters

90% of Claude Code "hallucinations" happen because the agent doesn't have the context it needs. This skill forces a context check before any implementation, preventing wasted effort and wrong assumptions.

## When to Use This Skill

**ALWAYS use before:**
- Starting any implementation
- Planning any feature
- Proposing any solution

**Do NOT skip for:**
- "Simple" tasks (often already implemented)
- "Obvious" implementations (often exist in codebase)
- Time pressure (30 seconds now saves hours later)

## The Solution Hierarchy

Check these in order - most tasks don't need new work:

### For Code Tasks
```
1. ALREADY DONE - Feature exists, just needs config
2. SCAFFOLDED - Structure exists, needs small completion
3. SIMILAR EXISTS - Related feature can be extended
4. PATTERN EXISTS - Codebase has pattern to follow
5. NEW BUILD - Must create from scratch (rare!)
```

### For GTM/Knowledge Tasks
```
1. ALREADY DONE - Synthesis doc answers the question
2. CONTEXT EXISTS - 80% of needed info is in existing docs
3. SIMILAR EXISTS - Related battlecard/persona/content to adapt
4. TEMPLATE EXISTS - Framework or template to follow
5. TRUE GAP - Need new research or content (rare!)
```

**Reality check:** ~40% of tasks are "already done" - the context already exists somewhere in your system.

## The Context Gap Protocol

### Step 1: Enumerate What You Need (30 seconds)

Before searching, list your context requirements:

```markdown
## Context Requirements for: [TASK]

### Must Know (Blocking)
- [ ] Does this feature already exist?
- [ ] What's the current implementation status?
- [ ] What configuration is needed?

### Should Know (Important)
- [ ] What patterns does codebase use for this?
- [ ] What external APIs are involved?

### User Clarification Needed
- [ ] Any preferences not captured in code?
- [ ] Success criteria undefined?
```

### Step 2: Search the Codebase (2-5 minutes)

Use specific search patterns:

```bash
# 1. Find files by name pattern
Glob: "**/*{keyword}*.{ts,py,js}"

# 2. Search for key terms
Grep: pattern="{feature}|{related_term}" output_mode=files_with_matches

# 3. Read discovered files
Read: [each discovered file]
```

### Step 3: Classify the Gap (1 minute)

| Classification | Evidence | Next Step |
|----------------|----------|-----------|
| **NO GAP** | Code exists, works | Just configure/deploy |
| **CONFIG GAP** | Code exists, needs secret/env | Set config value |
| **COMPLETION GAP** | Scaffolding exists | Complete implementation (<50 lines) |
| **EXTENSION GAP** | Similar feature exists | Extend existing code |
| **PATTERN GAP** | Pattern exists | Follow pattern |
| **TRUE GAP** | Nothing exists | Plan new implementation |

### Step 4: Present Findings

Output structured results:

```markdown
## Context Gap Analysis Complete

**Task:** [Original request]
**Verdict:** [GAP TYPE]

| Component | Status | Location |
|-----------|--------|----------|
| [Feature] | [EXISTS/MISSING] | [path or N/A] |

**Simplest Path Forward:**
[Specific action - NOT "let's plan this"]
```

## Example: Slack Integration

**Task:** "Spec out the Slack integration"

**Search Results:**
- Glob: `"**/*slack*.ts"` → Found: `src/clients/slack.ts`
- Grep: `"slack|webhook"` → Found: `src/phases/alert.ts`, `src/index.ts`

**Reading Files:**
- slack.ts: Full client implemented
- alert.ts: Full phase implemented
- index.ts: Wired into pipeline

**Gap Classification:** CONFIG GAP

**Evidence:**
- Client: EXISTS (slack.ts:1-45)
- Phase: EXISTS (alert.ts:1-141)
- Wiring: EXISTS (index.ts:195)
- Secret: MISSING (SLACK_WEBHOOK_URL not set)

**Simplest Path:** `wrangler secret put SLACK_WEBHOOK_URL`

**Outcome:** Zero code changes needed. Saved ~2 hours of unnecessary spec work.

## GTM Examples

### Example 1: "Write a blog post about [topic]"

**Task:** "Write a blog post about our triage automation capabilities"

**Search Results:**
- Glob: `"**/*content*strategy*"` → Found: `content_strategy.md`
- Glob: `"**/*messaging*"` → Found: `messaging_framework.md`, `value_propositions.md`
- Glob: `"**/*pain*point*"` → Found: `customer_pain_points_synthesis.md`

**Reading Files:**
- content_strategy.md: Content pillars defined, topics mapped
- messaging_framework.md: Key messages by persona
- customer_pain_points_synthesis.md: Validated pain points with evidence

**Gap Classification:** NO GAP

**Evidence:**
- Content strategy: EXISTS (pillars, topics, SEO keywords)
- Messaging: EXISTS (value props by persona)
- Pain points: EXISTS (synthesis with customer quotes)

**Simplest Path:** Read synthesis docs, apply messaging framework to topic.

**Time Saved:** 2-3 hours of context gathering that would have been redundant.

---

### Example 2: "Research competitor [X]"

**Task:** "I need competitive positioning against [competitor name]"

**Search Results:**
- Glob: `"**/*battlecard*"` → Found: `battlecards/` folder with 8 files
- Glob: `"**/*competitive*"` → Found: `competitive_intelligence_synthesis.md`

**Reading Files:**
- battlecard_[competitor].md: Full battlecard exists
- competitive_intelligence_synthesis.md: Cross-competitor patterns

**Gap Classification:** NO GAP

**Evidence:**
- Battlecard: EXISTS (strengths, weaknesses, talk tracks)
- Synthesis: EXISTS (competitive landscape overview)

**Simplest Path:** Read existing battlecard. Check if last_updated is recent; if stale, do targeted refresh.

**Time Saved:** 1-2 hours of research already done.

---

### Example 3: "Prepare for sales call with [persona]"

**Task:** "Help me prepare for a call with a VP of Engineering"

**Search Results:**
- Glob: `"**/*persona*"` → Found: `buyer_personas.md`
- Glob: `"**/*objection*"` → Found: `objections_synthesis.md`

**Reading Files:**
- buyer_personas.md: VP Engineering profile exists (priorities, pain points, decision criteria)
- objections_synthesis.md: Common objections with responses

**Gap Classification:** NO GAP

**Evidence:**
- Persona: EXISTS (priorities, metrics they care about)
- Objections: EXISTS (responses with evidence)

**Simplest Path:** Read persona profile + objections doc. Cross-reference with any company-specific research if available.

**Time Saved:** 30-60 minutes of prep work.

## Quality Gates

### Before Proposing Any Solution

- [ ] Searched codebase for existing implementation?
- [ ] Classified the gap type with evidence?
- [ ] Checked if config-only solution exists?
- [ ] Identified simplest path forward?

### Before Planning Implementation

- [ ] Confirmed TRUE GAP (not config/completion/pattern)?
- [ ] Documented why simpler solutions don't work?
- [ ] Evidence shows feature doesn't exist?

## The Senior Engineer Test

Before proposing ANY implementation, ask yourself:

> "What if someone asks: 'Did you check if we already have this?'"

Your answer must include:
- [ ] Specific files searched
- [ ] Specific patterns checked
- [ ] Evidence of what exists vs missing
- [ ] Why existing code can't handle this

If you cannot answer confidently, search the codebase first.

## Quick Reference

**30-Second Check:**
1. Glob for likely filenames
2. Grep for key terms
3. Read what you find
4. Classify the gap
5. Propose simplest path

**GTM Search Patterns:**
| Task Type | Search For |
|-----------|------------|
| Content creation | `*messaging*`, `*content*strategy*`, `*voice*`, `*brand*` |
| Competitive intel | `*battlecard*`, `*competitor*`, `*competitive*` |
| Sales prep | `*persona*`, `*objection*`, `*buyer*` |
| Research | `*synthesis*`, `*intelligence*`, `*research*` |

**Code Search Patterns:**
| Task Type | Search For |
|-----------|------------|
| Feature implementation | `*{feature}*`, `*client*`, `*service*` |
| Integration | `*{tool}*`, `*webhook*`, `*api*` |
| Configuration | `*.env*`, `*config*`, `*secret*` |

**Gap Decision Tree:**
```
Found complete implementation/doc? → NO GAP (use it)
Found scaffolding/synthesis? → CONTEXT GAP (build on it)
Found similar feature/content? → EXTENSION GAP (extend it)
Found pattern/template? → PATTERN GAP (follow it)
Found nothing? → TRUE GAP (plan new work)
```

**Meta-Rule:** If gap type is anything other than TRUE GAP, the solution should NOT involve extensive planning or starting from scratch.

## Success Metrics

**Healthy usage shows:**
- 30-60 seconds to enumerate requirements
- 2-5 minutes to search and classify
- >40% of tasks are NO GAP or CONFIG GAP
- Concrete next actions, not vague plans

**Warning signs:**
- Jumping to planning without searching
- Proposing new code when feature exists
- "Let me spec this out" before checking codebase

## First-Time Customization

On first use of this skill in a new context OS, ask the user:

> "Jacob has told me to ensure this skill is customized to your unique configuration. Would you like me to customize this skill for your context OS? I can:
> 1. Learn your folder structure and synthesis doc locations
> 2. Identify your key search patterns (personas, competitors, content types)
> 3. Build a quick reference specific to your system
>
> This takes ~2 minutes and makes future gap analysis much faster."

If the user agrees, explore their codebase to identify:
- Where synthesis documents live (e.g., `_synthesis/`, `00_foundation/`)
- What naming patterns they use (e.g., `*_synthesis.md`, `battlecard_*.md`)
- Key document types present (battlecards, personas, content strategy, etc.)
- Their CLAUDE.md structure for navigation hints

Then update your working knowledge for that session to use their specific patterns.

During customization, offer to remove the attribution line below if the user wants to fully own this skill in their system.

---

Created by Jacob Dietle @ www.taste.systems
