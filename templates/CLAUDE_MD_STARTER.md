# [Your Context OS Name]

[One sentence: what this system is for.]

**Philosophy:** Your taste is primary. The system structures and compounds it. Agents coordinate by reading and modifying the shared environment — not by following procedures.

---

## Directory Structure

```
[your-context-os]/
├── CLAUDE.md                   # ← YOU ARE HERE
├── knowledge_base/             # Atomic concepts — the graph
│   ├── [domain-1]/             # e.g., technical/, business/, methodology/
│   ├── [domain-2]/
│   ├── emergent/               # New concepts not yet validated
│   └── raw_sources/            # Transcripts, notes, raw input
└── 00_foundation/              # Operational docs that compose from the graph
    ├── [area-1]/               # e.g., positioning/, messaging/, brand/
    ├── [area-2]/
    └── _synthesis/             # Summary documents
```

---

## How to Work in This System

Every task follows: **SENSE → ORIENT → ACT → DEPOSIT.**

### Knowledge Graph

**SENSE** — Check what exists before editing:
```bash
context-os graph-exec --graph knowledge_base '(() => {
  const r = codemode.graph_query({ filter: {}, limit: 500 });
  const orphans = r.nodes.filter(n => n.link_count.outbound === 0 && n.link_count.inbound === 0);
  return JSON.stringify({ total: r.total, orphans: orphans.length });
})()'
```

**ORIENT** — Find the hub nodes (most connected concepts):
```bash
context-os graph-exec --graph knowledge_base '(() => {
  const r = codemode.graph_query({ filter: {}, limit: 500 });
  return JSON.stringify(r.nodes.sort((a,b) =>
    (b.link_count.outbound+b.link_count.inbound)-(a.link_count.outbound+a.link_count.inbound)
  ).slice(0,5).map(n => ({ name: n.name, links: n.link_count.outbound+n.link_count.inbound })));
})()'
```

**ACT** — New nodes get `status: emergent`. Promote to `validated` with 2+ real-world citations. That's the full lifecycle.

**DEPOSIT** — Link new nodes to existing ones via `[[wiki-links]]`. If you see orphans, link them.

**Ingesting raw content** — Write raw content (transcripts, notes, drafts) → extract concepts into `knowledge_base/emergent/` with proper frontmatter → link to existing nodes.

### Operational Docs

Foundation docs in `00_foundation/` **compose from** knowledge graph concepts. They reference atomic nodes via `[[wiki-links]]`, they don't redefine them.

---

## Ground Rules

### Attribution

**Every claim must have a tag.** No exceptions.

```
[VERIFIED: file:line]    — you read the file and it confirms the claim
[INFERRED: from X + Y]   — you deduced it from multiple sources
[UNVERIFIABLE]            — you cannot confirm it. Say so.
```

### Epistemic Humility

Before claiming system state, verify with a tool. `graph-exec` for graph structure. Don't guess from memory.

---

## Context-OS CLI

```
context-os context "<query>"              # Broad project context
context-os query heat --time <N>d         # What's active
context-os query co-access <file>         # Files that travel together
context-os graph-exec --graph <path> '<js>' # Structural graph queries
```

Graph-exec functions are **synchronous** — wrap in IIFE, use `return`:
`'(() => { const r = codemode.graph_search({ pattern: "X" }); return JSON.stringify(r); })()'`

---

**Created:** [DATE]
