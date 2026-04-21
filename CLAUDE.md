# GTM Context OS Quickstart

**Purpose:** Help users build their first context operating system in 10 minutes.

---

## What This Plugin Does

Provides guided setup for creating a Context OS — a structured knowledge graph where AI compounds intelligence over time. The system uses stigmergic coordination: agents read and modify the shared environment, knowledge compounds through use.

### Skills

Claude Code discovers and fires these based on intent — no slash commands needed.

| Skill | When it fires |
|-------|---------------|
| `quickstart` | User wants 10-minute guided setup of a new Context OS |
| `ingest` | User wants raw content (transcripts, docs, notes) turned into knowledge nodes |
| `context-os-basics` | Foundation patterns for Context OS |
| `context-os-cli` | Query work context, file activity, heat metrics, and graph structure |
| `context-gap-analysis` | Check what already exists before building |
| `epistemic-context-grounding` | Ground decisions in domain knowledge before designing |
| `eval-loop` | Iterate systematically toward a quality target with automated backpressure |
| `coordinated-agent-teams` | Decompose a spec into a multi-agent implementation plan |

---

## Quick Start

Say "run the quickstart" to begin — the `quickstart` skill fires. The guide will:

1. Ask what your context OS is for (GTM, Product, Research)
2. Create the two-layer directory structure (knowledge graph + operational docs)
3. Generate CLAUDE.md navigation guide with context-os CLI commands
4. Process your first piece of content into a structured knowledge node
5. Verify it works

Total time: ~10 minutes

---

## How Context OS Works

### Two-Layer Architecture

**Knowledge Graph** (knowledge_base/)
- Atomic, reusable concepts with frontmatter metadata
- Linked via [[wiki-links]] — the graph has structure
- Lifecycle: `emergent` → `validated` (2+ citations) → `canonical`

**Operational Docs** (00_foundation/)
- Strategic documents that compose from the graph
- They reference atomic concepts, they don't redefine them

### Key Principle: SENSE → ORIENT → ACT → DEPOSIT

Every interaction follows this loop:
- **SENSE** — Check what exists (graph-exec, heat queries)
- **ORIENT** — Find hub nodes, read coordination surfaces
- **ACT** — Create/update content
- **DEPOSIT** — Link to existing nodes, reinforce the graph through use

### Context-OS CLI

The CLI provides structural queries over your knowledge graph:

```bash
context-os graph-exec --graph knowledge_base '<js>'  # Structural graph queries
context-os query heat --time <N>d                     # What's active
context-os context "<query>"                          # Broad project context
```

---

## Templates Included

| Template | Purpose |
|----------|---------|
| `CLAUDE_MD_STARTER.md` | Navigation guide with CLI integration |
| `node_template.md` | Knowledge node frontmatter format |
| `sample-transcript.md` | Example content for testing ingestion |

---

## Learn More

For advanced patterns (multi-agent orchestration, client engagement systems, enterprise context architectures):
https://taste.systems
