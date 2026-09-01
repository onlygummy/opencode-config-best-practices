---
name: domain-modeling
description: "Build and sharpen project's domain model in Obsidian vault using MCP tools. Creates glossary and ADRs via structured editing."
license: MIT
metadata:
  version: "1.2.0"
  author: "Onlygummy"
  platforms: "linux, macos, windows"
  tags: "domain-modeling, obsidian, mcp, glossary, adr, structured-editing"
  related_skills: "grilling, second-brain, grill-with-docs"
---

# Domain Modeling (MCP)

Build and sharpen the project's domain model using Obsidian MCP tools for structured editing.

## When to Use

- During planning when domain terms are resolved
- When architectural decisions meet ADR criteria (hard to reverse + surprising + real trade-off)
- When user says "add glossary term", "create ADR", or "update glossary"
- After Grilling Mode completes and decisions are settled

## File Structure

```
vault/
├── 04 Memory/
│   └── [project]/
│       ├── glossary.md
│       └── adr/
│           ├── 0001-slug.md
│           └── 0002-slug.md
```

## MCP Tools Reference

| Tool | Purpose | When to Use |
|------|---------|-------------|
| `obsidian_vault_list` | List files/directories | Check if file exists |
| `obsidian_vault_read` | Read file + metadata | Read glossary before update |
| `obsidian_vault_get_document_map` | Get heading tree + block IDs + version | Know structure before patch |
| `obsidian_vault_patch` | Structured edit (heading/block/frontmatter) | Update glossary term by term |
| `obsidian_vault_write` | Write entire file | Create new files (ADR, glossary) |
| `obsidian_vault_delete` | Delete file | Remove deprecated ADR |

## Glossary Workflow

### Step 1: Check if file exists

```
obsidian_vault_list("vault/04 Memory/[project]/")
```

If glossary.md does not exist, create it with `obsidian_vault_write`.

### Step 2: Read current glossary

```
obsidian_vault_read("vault/04 Memory/[project]/glossary.md")
```

### Step 3: Get document map (heading structure + version)

```
obsidian_vault_get_document_map("vault/04 Memory/[project]/glossary.md")
```

Returns:
- `headings`: e.g., `{ "Language": {} }`
- `version`: e.g., `"abc123"` (use for optimistic concurrency)

### Step 4: Add or update term

**Add new term** (if term does not exist):

```
obsidian_vault_patch(
  path: "vault/04 Memory/[project]/glossary.md",
  targetType: "heading",
  target: ["Language"],
  operation: "append",
  content: "\n\n**{Term}**:\n{Definition}\n_Avoid_: {Alternatives}",
  ifMatch: "abc123"
)
```

**Update existing term** (if term already exists):

```
obsidian_vault_patch(
  path: "vault/04 Memory/[project]/glossary.md",
  targetType: "heading",
  target: ["Language", "{Term}"],
  operation: "replace",
  content: "**{Term}**:\n{New Definition}\n_Avoid_: {New Alternatives}",
  ifMatch: "abc123"
)
```

## ADR Workflow

### Step 1: Check if adr/ exists

```
obsidian_vault_list("vault/04 Memory/[project]/adr/")
```

### Step 2: Create ADR (lazy creation)

```
obsidian_vault_write(
  path: "vault/04 Memory/[project]/adr/{NNNN}-{slug}.md",
  content: "# {Short title}\n\n{1-3 sentences: context, decision, why.}"
)
```

### Step 3: Update ADR (if needed)

```
obsidian_vault_patch(
  path: "vault/04 Memory/[project]/adr/{NNNN}-{slug}.md",
  targetType: "heading",
  target: ["{Section}"],
  operation: "replace",
  content: "{New content}"
)
```

## Rules

- Use `obsidian_vault_patch` for updates (not write)
- Use `ifMatch` from document map for optimistic concurrency
- Create files lazily (only when content exists)
- ADR criteria: hard to reverse + surprising + real trade-off
- Glossary: define what it IS, not what it does
- Avoid general programming concepts (only project-specific terms)
- One or two sentences per term
- Be opinionated: pick one word, list alternatives under `_Avoid_`
