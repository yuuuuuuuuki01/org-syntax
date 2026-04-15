# org-syntax

A Claude Code skill for **law-governed AI agent organizations** that structures Claude's responses as multi-department analysis followed by a synthesized judgment.

## What Makes This Different

Most "multi-perspective" prompts ask Claude to *roleplay* as departments. This skill inverts that:

- **Claude does not generate department views.** Perspectives are pre-computed upstream (scripts, Gemini, Codex) and injected as `<org_analysis>` XML tags.
- **Claude's role is synthesis only.** It sits at the end of a cost-tiered pipeline — `scripts → Gemini → Codex → Claude` — and handles only what can't be delegated.
- **Departments are canonical.** Names come from the organization's live state (`org_state.json`), not invented by Claude.
- **Law hierarchy is enforced.** If a department perspective conflicts with constitution-level rules, the synthesis flags it. Department outputs cannot override higher law.
- **Delegation mode skips reprocessing.** When `<delegation>` is present, Claude renders judgment directly without re-reading source material — saves tokens.

## Response Format

When `<org_analysis>` is in context, Claude responds as:

```
【部署名】
〇〇の観点から...(2〜4文)

【部署名】
〇〇の観点から...(2〜4文)

---
（Claudeとしての最終判断・統合）
```

Only relevant departments are included. The `---` divider marks the boundary between department input and Claude's original synthesis.

## Install

```bash
claude plugin install https://github.com/yuuuuuuuuki01/org-syntax
```

Or clone manually:

```bash
git clone https://github.com/yuuuuuuuuki01/org-syntax ~/.claude/plugins/org-syntax
```

## Usage

The skill activates automatically when:
- Context contains `<org_analysis>` tag
- User asks to respond "in org format" / "部署フォーマットで"

### Delegation mode

When `<delegation>` tag is present, Claude skips re-reading source material and renders only the final judgment from pre-processed output.

## Architecture Fit

```
Task
 └── Scripts (free)
      └── Gemini (cheap, bulk processing)
           └── Codex (mid, code generation)
                └── Claude ← org-syntax renders here
```

The `<org_analysis>` tag is the handoff point into Claude. This keeps Claude's token usage minimal while preserving organizational judgment quality at the synthesis layer.

## License

MIT
