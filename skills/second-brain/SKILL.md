---
name: second-brain
description: "Manage second brain vault — create notes from templates, organize knowledge, link related notes."
license: MIT
metadata:
  version: "1.0.0"
  author: "Onlygummy"
  platforms: "linux, macos, windows"
  tags: "second-brain, knowledge-management, templates, best-practices, lessons-learned"
---

# Second Brain

Manage knowledge in the second brain vault with consistent templates and conventions.

## When to Use

- Creating new best practice note
- Creating new lesson learned note
- Creating new framework note
- Creating new library note
- Creating glossary for a project
- Creating ADR for a project
- Searching for related notes
- Organizing and linking notes

## Note Types

| Type | Folder | Template |
|------|--------|----------|
| Best Practice | `00 Best Practices/[Category]/` | `best-practice.md` |
| Lesson Learned | `01 Lessons Learned/[Category]/` | `lesson-learned.md` |
| Framework | `02 Frameworks/[Category]/` | `framework.md` |
| Library | `03 Libraries/[Category]/` | `library.md` |
| Glossary | `04 Memory/[project]/` | `glossary.md` |
| ADR | `04 Memory/[project]/adr/` | `adr.md` |

## Vault Structure

```
vault/
├── 00 Best Practices/      # Best practices
├── 01 Lessons Learned/     # Lessons learned
├── 02 Frameworks/          # Frameworks
├── 03 Libraries/           # Libraries
├── 04 Memory/              # Per-project memory
│   └── [project]/
│       ├── glossary.md
│       └── adr/
└── Home.md                 # Dashboard
```

## Workflow

### Create Note

1. Ask user what type of note to create
2. Select appropriate template from `templates/` folder
3. Create note in correct folder using `obsidian_vault_write`
4. Fill in template from user input
5. Link to related notes using `[[wikilinks]]`

### Search Notes

1. Use `obsidian_vault_search` to find related notes
2. Present results to user
3. Suggest notes to link

## Templates

| Template | File |
|----------|------|
| Best Practice | `templates/best-practice.md` |
| Lesson Learned | `templates/lesson-learned.md` |
| Framework | `templates/framework.md` |
| Library | `templates/library.md` |
| Glossary | `templates/glossary.md` |
| ADR | `templates/adr.md` |

## Conventions

- Use `[[wikilinks]]` to connect notes
- Add frontmatter to every note
- One idea per note (atomic)
- Link related notes
- Use templates for consistency
- Review notes quarterly
- Write all content in English (no Thai or other languages)

## Rules

- Read template before creating note
- Use `obsidian_vault_write` for new notes
- Use `obsidian_vault_patch` for updates
- Always link to at least one related note
- Keep frontmatter consistent
