# OpenCode Config Best Practices

> Minimal, readable OpenCode configuration — language-aware agents, Clean Code principles, and reusable skills. No hardcoded secrets, no MCP, no second brain.

## Agents

All agents are accessible via **tab switch** in the TUI.

| Tab | Agent | Mode | Capability |
|-----|-------|------|------------|
| **Plan** (default) | Systems Architect | primary | Requirement analysis, architecture design, work breakdown structure. Auto-calls `humanizer` before delivering any plan. |
| **Build** | Software Engineer | primary | Write and refactor code following Clean Code (SOC, DYC, DRY, KISS, TDD, YAGNI). Auto-calls `humanizer` for docs and UX copy. |
| **Review** | Code Review Specialist | primary | Independent pre-commit verification, security scan, quality gates, and Git Flow commit message generation. Uses `requesting-code-review` + `humanizer`. Read-only. |


- **Plan** is the default entry point — start here for architecture and planning. Uses readability mode with clear, complete sentences.
- **Build** handles implementation; switch to it when a plan is ready. Has `edit: allow` and `skill: allow`. Code review is handled by Review agent.
- **Review** is an independent reviewer that verifies `build`'s changes from a fresh context and generates Git Flow commit messages. Uses `requesting-code-review` skill and `humanizer` for report prose. Read-only.
- All agents auto-detect the user's language and respond accordingly.

## Skills

Skills are reusable `SKILL.md` files loaded on demand via the native `skill` tool. This config follows the `agentskills.io` standard and the locations described in https://opencode.ai/docs/th/skills.

| Skill | Purpose | Used by | Trigger |
|-------|---------|---------|---------|
| `humanizer` (v2.11.2) | Rewrite AI-sounding prose using 35 patterns from Wikipedia's Signs of AI writing. Keeps meaning, does not invent facts. | `plan`, `build` | Auto — `plan` after any plan longer than 3 paragraphs, `build` after docs/UX copy (file mode for files, embedded mode otherwise). Also available via `/humanizer` or natural language. |
| `requesting-code-review` (v2.0.0) | Pre-commit verification: `git diff`, security scan, tests/lint baseline, self-review checklist, independent review, auto-fix loop, then commit with Git Flow format. | `review` | Auto — `review` on tab switch when verifying `build`'s diff. Manual: `skill({ name: "requesting-code-review" })`. Uses `task` instead of Hermes `delegate_task`. Skip for docs-only or when user says "skip verification". |

Global skill folder (single source of truth):

```
~/.config/opencode/skills/<name>/SKILL.md          # OpenCode global — the only folder used in this config
```

Permissions are set in `opencode.jsonc`:

```jsonc
"permission": {
  "skill": {
    "*": "allow",
    "humanizer": "allow",
    "requesting-code-review": "allow"
  }
}
```

See https://opencode.ai/docs/th/skills for file placement, frontmatter rules (`name`, `description`, `license`, `compatibility`, `metadata` as string map), and discovery.

## Configuration

`opencode.jsonc` (57 lines) — minimal and explicit:

- `$schema: https://opencode.ai/config.json` with `default_agent: "plan"`
- `permission`: `edit: deny` globally (only `build` overrides), `read/glob/grep/webfetch: allow`, `skill` as above, `task: deny` globally (only `plan` overrides), `bash` read-only whitelist (`git *`, `rg *`, `cat/dir/ls/type`, `Get-ChildItem/Get-Content/Select-String/Test-Path/Write-Output/echo`) — only `build` has `bash: "*": "allow"`
- `lsp`: `typescript-language-server` for `.ts/.tsx`, `ty` via `uv run ty server` for `.py/.pyi`, `pyright` disabled
- `shell: "powershell"` for Windows
- No `provider` and no `mcp` — intentionally minimal, no hardcoded secrets

## File Structure

```
~/.config/opencode/
├── opencode.jsonc                 # Main config — schema, permissions, LSP, shell
├── skills/                        # Global skills — single source of truth (@skills\)
│   ├── humanizer/SKILL.md         # Remove AI writing patterns (35 patterns)
│   └── requesting-code-review/SKILL.md # Pre-commit security and quality gates
├── agents/
│   ├── plan.md                    # Plan — Systems Architect (readability + humanizer auto)
│   ├── build.md                   # Build — Software Engineer (humanizer + UX auto)
│   ├── review.md                  # Review — Code Review Specialist (verify + Git Flow commit, read-only)
└── package.json                   # @opencode-ai/plugin 1.17.4
```

Note: The legacy template referenced `learn.md`, `commit.md`, and `brain/` (Obsidian Vault). This config intentionally removes them — `learn` and second brain were removed with the `mcp` block, and `commit` logic was merged into `review`.

## Update

```bash
cd ~/.config/opencode/ && git pull
# After pulling new skills, restart OpenCode
```

## Related

- [OpenCode Documentation](https://opencode.ai/docs)
- [Agent Skills Standard](https://agentskills.io)
- [Humanizer Skill](https://github.com/blader/humanizer)
- [Hermes Agent Skills](https://github.com/NousResearch/hermes-agent/tree/main/skills)
