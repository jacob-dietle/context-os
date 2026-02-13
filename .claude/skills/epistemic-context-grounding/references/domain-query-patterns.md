# Domain Query Patterns

Search patterns for traversing your context OS to discover domain documentation, specs, and canonical sources.

## Search Order

When querying domain knowledge, follow this priority order:

```
1. specs/canonical/*.md       - Blessed architecture docs (highest authority)
2. specs/{project}/*.md       - Project-specific specs
3. **/CLAUDE.md               - Navigation guides (find paths)
4. **/README.md               - Component documentation
5. knowledge_base/**/*.md     - Knowledge graph nodes
```

**Note:** Adapt these paths to your context OS structure. The hierarchy matters more than the exact paths.

## Common Spec Locations

### Canonical Specs

**Purpose:** Blessed architecture documentation, data models, system specs

**Search patterns:**
```bash
# All canonical specs
Glob: "specs/canonical/*.md"

# Data models specifically
Glob: "**/*DATA_MODEL*.md"
Glob: "**/*SCHEMA*.md"

# System specs
Glob: "**/*SPEC*.md"
Glob: "**/*ARCHITECTURE*.md"
```

### Project-Specific Specs

**Purpose:** Component-specific specifications, feature specs

**Search patterns:**
```bash
# Find project specs directories
Glob: "**/specs/**/*.md"

# Find context packages (contain state + specs references)
Glob: "**/context_packages/**/*.md"
```

### Navigation Guides

**Purpose:** Entry points for navigating subdirectories

**Search patterns:**
```bash
# Find all CLAUDE.md files
Glob: "**/CLAUDE.md"

# Start with root
Read: "CLAUDE.md"
```

**CLAUDE.md provides:**
- Directory structure
- Key file locations
- Navigation rules
- Quick start commands

### Component Documentation

**Purpose:** Component-level documentation

**Search patterns:**
```bash
# Find all READMEs
Glob: "**/README.md"
```

### Knowledge Graph Nodes

**Purpose:** Atomic concepts, validated patterns, methodology

**Search patterns:**
```bash
# All knowledge nodes
Glob: "knowledge_base/**/*.md"

# By domain (adapt to your taxonomy)
Glob: "knowledge_base/technical/*.md"
Glob: "knowledge_base/business/*.md"
Glob: "knowledge_base/methodology/*.md"
```

## Search Commands by Task Type

### Finding Data Format Specs

```bash
# Primary search
Glob: "**/*DATA_MODEL*.md"
Glob: "**/*SCHEMA*.md"
Glob: "specs/canonical/*data*.md"

# Secondary search
Grep: pattern="record type|message type|json" path="specs/"
Grep: pattern="schema|format|structure" path="specs/"
```

### Finding Integration Specs

```bash
# Primary search
Glob: "**/*integration*.md"
Glob: "**/*api*.md"
Glob: "specs/canonical/integrations*.md"

# Secondary search
Grep: pattern="API|endpoint|webhook" path="specs/"
Grep: pattern="OAuth|auth|token" path="specs/"
```

### Finding Architecture Docs

```bash
# Primary search
Glob: "**/*ARCHITECTURE*.md"
Glob: "**/*DESIGN*.md"
Glob: "specs/canonical/*architecture*.md"

# Secondary search
Grep: pattern="architecture|design|pattern" path="specs/"
```

### Finding Feature Specs

```bash
# Primary search
Glob: "**/specs/**/*SPEC*.md"
Glob: "**/context_packages/**/*.md"

# Search by feature name
Grep: pattern="[feature-name]" path="specs/"
```

## Grep Patterns for Content Discovery

### Finding Domain Terms

```bash
# Search for specific term
Grep: pattern="[domain-term]" path="specs/"
Grep: pattern="[domain-term]" path="knowledge_base/"

# Case insensitive
Grep: pattern="(?i)[domain-term]" path="specs/"
```

### Finding Definitions

```bash
# Look for definition patterns
Grep: pattern="^##.*[term]|^###.*[term]" path="specs/"
Grep: pattern="defines|definition|means" path="specs/"
```

### Finding Evidence

```bash
# Look for validation/evidence
Grep: pattern="VERIFIED|GROUNDED|evidence" path="specs/"
Grep: pattern="validated_by|proven" path="knowledge_base/"
```

## CLAUDE.md Navigation Patterns

### Start with Root

```bash
Read: "CLAUDE.md"

# Provides:
# - System overview
# - Directory structure
# - Navigation rules
# - Quick start
```

### Follow to Project

```bash
# After reading root CLAUDE.md, follow references
Read: "[project]/CLAUDE.md"         # Project-specific
Read: "00_foundation/CLAUDE.md"     # Foundation layer
```

### Extract Paths

CLAUDE.md typically contains:
```markdown
## Project Structure

specs/canonical/*.md - Blessed architecture docs
specs/{project}/*.md - Project-specific specs
```

Use these paths for targeted searches.

## Wiki-Link Discovery

### Following Wiki-Links

Knowledge graph uses `[[wiki-links]]` for references:

```markdown
See [[data-model]] for record format.
Related: [[context-engineering]]
```

**Pattern:** When you see `[[node-name]]`:
1. Search for the node: `Glob: "**/*[node-name]*.md"`
2. Read the referenced file
3. Follow further links if needed

### Discovering Related Content

```bash
# Find files referencing a concept
Grep: pattern="\[\[[concept-name]\]\]" glob="**/*.md"

# Find backlinks
Grep: pattern="[concept-name]" path="knowledge_base/"
```

## Tag-Based Discovery

### Using taxonomy.yaml

```bash
# Read taxonomy to understand tag structure
Read: "taxonomy.yaml"

# Search by tag
Grep: pattern="tags:.*[tag-name]" glob="knowledge_base/**/*.md"
```

## Quick Reference: 30-Second Search

For any new domain task:

```bash
# 1. Canonical specs (15 seconds)
Glob: "specs/canonical/*.md"
Glob: "**/*DATA_MODEL*.md"

# 2. Project specs (10 seconds)
Glob: "**/specs/**/*.md"
Read: "CLAUDE.md"

# 3. Domain terms (5 seconds)
Grep: pattern="[key-term]" path="specs/"
```

If found -> Read and ground solution
If not found -> Search is VERIFIED as negative (document the search)

## Output Format

After domain query, document findings:

```markdown
## Domain Knowledge Query Results

**Search performed:** [list of commands]

**Specs found:**
- [[spec-file-1]] - [relevance and key info]
- [[spec-file-2]] - [relevance and key info]

**Relevant content:**
- Key fact 1 [GROUNDED: spec:line-range]
- Key fact 2 [GROUNDED: spec:line]

**Not found:**
- No spec for X (searched: specs/, knowledge_base/)

**Conclusion:**
- Solution should use: [approach based on findings]
- Assumptions verified: [X of Y]
```
