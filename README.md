# OpenCode Config Best Practices

> Minimal, readable OpenCode configuration - language-aware agents, Clean Code principles, reusable skills, and domain modeling with Obsidian vault. No hardcoded secrets.

## Quick Start

Get running in 3 steps:

1. **Clone this config:**

   ```powershell
   cd ~/.config/opencode && git clone <repo-url> .
   ```

2. **Set up your Obsidian API key:**

   ```powershell
   cp .secrets/obsidian-api-key.example .secrets/obsidian-api-key
   # Edit .secrets/obsidian-api-key with your key from Obsidian > Settings > Community plugins > MCP
   ```

3. **Restart OpenCode.** The `plan` agent is your default entry point

## Overview

This configuration provides:

- **Language-aware agents:** Plan, Build, Review with specialized roles
- **Reusable skills:** Humanizer, Code Review, Grilling, Domain Modeling
- **Domain modeling:** Glossary + ADRs in Obsidian vault
- **Clean Code principles:** SOC, DYC, DRY, KISS, TDD, YAGNI
- **Security-first:** No hardcoded secrets, minimum privilege per agent

## Agents

All agents are accessible via **tab switch** in the TUI.

| Tab                | Agent                  | Purpose                 | Key Capability                   |
| ------------------ | ---------------------- | ----------------------- | -------------------------------- |
| **Plan** (default) | Systems Architect      | Architecture & planning | Grilling Mode + Domain Modeling  |
| **Build**          | Software Engineer      | Implementation          | Clean Code + Terse Mode          |
| **Review**         | Code Review Specialist | Pre-commit verification | Security scan + Git Flow commits |

### Plan (Default Entry Point)

- **When to use:** Start here for architecture, requirements, planning
- **Auto-behavior:** Detects fuzzy requirements → enters Grilling Mode
- **Creates:** Glossary (`vault/04 Memory/[project]/glossary.md`) + ADRs
- **Calls:** `humanizer` after any plan > 3 paragraphs

### Build

- **When to use:** When a plan is ready for implementation
- **Permissions:** `edit: allow`, `bash: "*": "allow"`
- **Default mode:** Terse (explanations compressed, fragments OK)
- **Auto-behavior:** Calls `humanizer` for docs/UX copy

### Review

- **When to use:** After Build completes, before commit
- **Permissions:** Read-only (`edit: deny`)
- **Workflow:** Runs `requesting-code-review` skill → 8-step pipeline → Git Flow commit message

All agents auto-detect the user's language and respond accordingly.

## Skills

Skills are reusable `SKILL.md` files loaded on demand via the native `skill` tool. This config follows the `agentskills.io` standard and the locations described in https://opencode.ai/docs/th/skills.

| Skill                    | Purpose                                                | Used By     |
| ------------------------ | ------------------------------------------------------ | ----------- |
| `humanizer`              | Rewrite AI-sounding prose (35 patterns from Wikipedia) | Plan, Build |
| `requesting-code-review` | Pre-commit: security scan, quality gates, auto-fix     | Review      |
| `grilling`               | Structured interview for requirements clarification    | Plan        |
| `domain-modeling`        | Build glossary + ADRs in Obsidian vault                | Plan        |
| `grill-with-docs`        | Combined `grilling` + `domain-modeling`                | Plan        |
| `second-brain`           | Knowledge management (templates + conventions)         | Plan, Build |

### Skill Locations

Global skill folder (single source of truth):

```
~/.config/opencode/skills/<name>/SKILL.md
```

See https://opencode.ai/docs/th/skills for placement rules and frontmatter standards.

### Permissions

Configured in `opencode.jsonc`:

```jsonc
"skill": {
  "*": "allow",
  "humanizer": "allow",
  "requesting-code-review": "allow",
  "grilling": "allow",
  "domain-modeling": "allow",
  "grill-with-docs": "allow",
  "second-brain": "allow"
}
```

## Grilling Mode

When Plan agent detects fuzzy requirements, it automatically enters Grilling Mode.

### Detection Triggers

- Fuzzy terms: "ระบบ", "จัดการ", "แบบเดิม", "ดีกว่า", "Something like..."
- Missing scope: no user types, no edge cases, no success criteria
- Missing context: no tech constraints, no team constraints, no timeline

### Workflow

1. Call `skill({ name: "grill-with-docs" })`
2. Round 1: 5-7 frontier questions with recommended answers
3. Wait for user answers
4. Round 2+: Recompute frontier, ask new questions
5. Exit: Frontier empty OR user says "skip grilling"

### Exit Commands

"skip grilling", "enough", "ข้าม", "พอแล้ว"

## Domain Modeling

### Vault Structure

```
vault/
├── 00 Best Practices/
├── 01 Lessons Learned/
├── 02 Frameworks/
├── 03 Libraries/
├── 04 Memory/
│   └── [project]/
│       ├── glossary.md
│       └── adr/
└── Home.md
```

### ADR Criteria

Create ADR when ALL three criteria are met:

1. **Hard to reverse:** Changing mind later has meaningful cost
2. **Surprising without context:** Future reader will wonder "why?"
3. **Real trade-off:** There were genuine alternatives

Skip ADR for: easy decisions, obvious choices, reversible changes.

## Configuration

`opencode.jsonc` is minimal and explicit:

| Setting         | Value                             | Purpose               |
| --------------- | --------------------------------- | --------------------- |
| `$schema`       | `https://opencode.ai/config.json` | IDE validation        |
| `default_agent` | `plan`                            | Entry point           |
| `shell`         | `powershell`                      | Windows               |

### Permissions

- **Global:** `edit: deny`, `read/glob/grep/webfetch: allow`, `task: deny`
- **Bash:** Read-only whitelist (`git *`, `rg *`, `cat/dir/ls/type`, `Get-ChildItem/Get-Content/Select-String/Test-Path/Write-Output/echo`). Only Build overrides to full allow.
- **Skills:** All allowed globally, per-skill rules above

### LSP

| Language     | Server                       | Extensions    |
| ------------ | ---------------------------- | ------------- |
| TypeScript   | `typescript-language-server` | `.ts`, `.tsx` |
| Python       | `ty` via `uv run ty server`  | `.py`, `.pyi` |
| Python (alt) | `pyright`                    | disabled      |

## Secrets Management

This config uses `{file:path}` interpolation to keep API keys separate from config files.

### Setup

1. Copy the example file:

   ```powershell
   cp .secrets/obsidian-api-key.example .secrets/obsidian-api-key
   ```

2. Add your Obsidian API key (from Obsidian > Settings > Community plugins > MCP):

   ```
   <your-api-key>
   ```

3. Restart OpenCode

### Notes

- `.secrets/` is in `.gitignore` (safe to share config with team)
- Each team member creates their own key
- Never commit actual key files (only `.example` files are committed)

## File Structure

```
~/.config/opencode/
├── opencode.jsonc                 # Main config (schema, permissions, LSP, shell)
├── .secrets/                      # API keys (gitignored, never commit)
│   ├── obsidian-api-key           # Your Obsidian MCP API key
│   └── obsidian-api-key.example   # Template for team members
├── skills/                        # Global skills (single source of truth)
│   ├── humanizer/SKILL.md         # Remove AI writing patterns (35 patterns)
│   ├── requesting-code-review/SKILL.md # Pre-commit security and quality gates
│   ├── grilling/SKILL.md          # Structured interview for requirements clarification
│   ├── domain-modeling/SKILL.md   # MCP-based domain modeling in Obsidian vault
│   ├── grill-with-docs/SKILL.md  # Meta-skill: grilling + domain-modeling
│   └── second-brain/SKILL.md     # Knowledge management (templates + conventions)
├── agents/
│   ├── plan.md                    # Plan (Systems Architect: Grilling Mode + Domain Modeling)
│   ├── build.md                   # Build (Software Engineer: humanizer + UX auto)
│   ├── review.md                  # Review (Code Review Specialist: verify + Git Flow commit, read-only)
├── vault/                         # Second brain (knowledge storage)
│   ├── 00 Best Practices/         # Best practices by category
│   ├── 01 Lessons Learned/        # Mistakes and anti-patterns
│   ├── 02 Frameworks/             # Framework reference
│   ├── 03 Libraries/              # Library reference
│   ├── 04 Memory/                 # Per-project memory (glossary + ADRs)
│   │   └── [project]/
│   │       ├── glossary.md
│   │       └── adr/
│   └── Home.md                    # Dashboard
└── package.json                   # @opencode-ai/plugin
```

## Update

```bash
cd ~/.config/opencode/ && git pull
# After pulling new skills, restart OpenCode
```

## Glossary

| Term              | Definition                                                                 |
| ----------------- | -------------------------------------------------------------------------- |
| **Grilling Mode** | Structured interview to clarify fuzzy requirements before planning         |
| **ADR**           | Architecture Decision Record (documents significant architectural choices) |
| **WBS**           | Work Breakdown Structure (hierarchical decomposition of project scope)     |
| **SOC**           | Separation of Concerns (each unit has one responsibility)                  |
| **DYC**           | Document Your Code (explain complex logic with comments)                   |
| **DRY**           | Don't Repeat Yourself (maximize reusability)                               |
| **KISS**          | Keep It Simple, Stupid (avoid unnecessary complexity)                      |
| **TDD**           | Test Driven Development (write tests first, then code)                     |
| **YAGNI**         | You Ain't Gonna Need It (build only essential features)                    |
| **Terse Mode**    | Build agent's default: compressed explanations, fragments OK               |
| **Git Flow**      | Branching model with prefixes: Feature, Bugfix, Hotfix, etc.               |

## Related

- [OpenCode Documentation](https://opencode.ai/docs)
- [Agent Skills Standard](https://agentskills.io)
- [Humanizer Skill](https://github.com/blader/humanizer)
- [Hermes Agent Skills](https://github.com/NousResearch/hermes-agent/tree/main/skills)
- [mattpocock/skills](https://github.com/mattpocock/skills) (grilling, domain-modeling, grill-with-docs)
