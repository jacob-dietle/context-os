# GTM Context OS Quickstart Plugin

**Purpose:** Help users build their first context operating system in 10 minutes.

---

## What This Plugin Does

This plugin provides guided setup for creating a Context OS - a structured knowledge system where AI compounds intelligence over time.

### Commands

| Command | Purpose |
|---------|---------|
| `/quickstart` | 10-minute guided setup experience |
| `/ingest` | Process raw content into knowledge graph |
| `/graph-health` | Check system health and get recommendations |

### Skills

| Skill | Purpose |
|-------|---------|
| `context-os-basics` | Foundation patterns for context OS |
| `context-gap-analysis` | Check what exists before building - prevents hallucinations |

---

## Quick Start

Run `/quickstart` to begin. The guide will:

1. Ask what your context OS is for (GTM, Product, Research)
2. Create appropriate directory structure
3. Generate CLAUDE.md navigation guide
4. Set up taxonomy and ontology rules
5. Process your first piece of content
6. Verify it works

Total time: ~10 minutes

---

## How Context OS Works

### Two-Layer Architecture

**Layer 1: Atomic Knowledge** (knowledge_base/)
- Individual reusable concepts
- Structured with frontmatter metadata
- Linked via [[wiki-links]]

**Layer 2: Operational Docs** (00_foundation/)
- Strategic documents that compose Layer 1
- Positioning, messaging, processes
- Reference atomic concepts, don't redefine them

### Key Files

- `CLAUDE.md` - Navigation guide (AI reads this first)
- `taxonomy.yaml` - Blessed tags for your domain
- `ontology.yaml` - How concepts relate to each other

---

## Templates Included

| Template | Purpose |
|----------|---------|
| `CLAUDE_MD_STARTER.md` | Navigation guide starter |
| `taxonomy_starter.yaml` | Basic tag structure |
| `ontology_starter.yaml` | Relationship definitions |
| `node_template.md` | Knowledge node format |
| `sample-transcript.md` | Example content for testing |

---

## Learn More

For advanced patterns (Chief of Staff orchestration, team governance, enterprise taxonomy):
https://taste.systems
