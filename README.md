# org-syntax

A Claude Code skill that structures responses as multi-department organizational analysis.

Designed for **law-governed AI agent organizations** where multiple departments each contribute their perspective on a decision, followed by a synthesized judgment from Claude.

## What It Does

When `<org_analysis>` context is present, Claude formats responses as:

```
【部署名】
〇〇の観点から...(2〜4文)

【部署名】
〇〇の観点から...(2〜4文)

---
（Claudeとしての最終判断・統合）
```

Only relevant departments are included. Each section is 2–4 sentences. Claude's integrated judgment follows the divider.

## Install

```bash
claude plugin install https://github.com/yuuuuuuuuki01/org-syntax
```

Or manually clone into your Claude plugins directory:

```bash
git clone https://github.com/yuuuuuuuuki01/org-syntax ~/.claude/plugins/org-syntax
```

## Usage

The skill activates automatically when:
- Context contains `<org_analysis>` tag
- User asks to respond "in org format"

### Delegation mode

When `<delegation>` tag is present, Claude receives pre-processed Gemini/Codex output and renders only the final judgment — no reprocessing, minimizing token cost.

## Design Principles

- **Cost-first**: fits into a pipeline where scripts → Gemini → Codex handle heavy lifting; Claude handles final synthesis only
- **Law-governed**: department perspectives are authoritative inputs, not suggestions
- **Concise**: 2–4 sentences per department prevents verbosity

## License

MIT
