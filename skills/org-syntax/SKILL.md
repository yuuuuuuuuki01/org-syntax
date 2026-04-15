---
name: org-syntax
description: This skill should be used when the context contains an <org_analysis> tag, when the user asks to respond in org format, or when working within a law-governed AI agent organization structure where multiple departments provide perspectives on a decision.
version: 1.0.0
---

# Org Syntax Skill

Structures responses as multi-department organizational analysis, followed by a synthesized judgment from Claude.

## What Makes This Different

Most "multi-perspective" prompting asks Claude to *roleplay* as departments. This skill is different:

- **Claude does not generate department views** — department perspectives are pre-computed upstream (by scripts, Gemini, or Codex) and injected via `<org_analysis>` tags
- **Claude's role is synthesis only** — it is the final stage in a cost-tiered pipeline: `scripts → Gemini → Codex → Claude`
- **Departments are canonical, not invented** — names come from the organization's live state (`org_state.json`), not from Claude's imagination
- **Law hierarchy is binding** — if a department's perspective conflicts with the constitution, the synthesis must flag it; department outputs cannot override higher law
- **Delegation mode eliminates reprocessing** — when `<delegation>` is present, Claude renders judgment directly from pre-processed output without re-reading source material

## When This Skill Applies

Activate this skill when:
- The context includes an `<org_analysis>` tag containing department perspectives
- The user asks to respond "in org format" or "部署フォーマットで"
- The conversation involves a decision that spans multiple organizational domains

## Response Format

When this skill is active, structure your response exactly as follows:

```
【部署名】
〇〇の観点から...(2〜4文)

【部署名】
〇〇の観点から...(2〜4文)

---
（Claudeとしての最終判断・統合）
```

## Rules

1. **Include only relevant departments** — do not force all departments into every response
2. **Each department section: 2–4 sentences** — reproduce or distill from `<org_analysis>`, do not invent
3. **Final synthesis paragraph** — Claude's integrated judgment after the `---` divider; this is the only place Claude adds original reasoning
4. **Use department names from `<org_analysis>`** — do not invent departments not present in context
5. **Law compliance check** — if any department perspective violates constitution-level rules, note it in the synthesis
6. **Delegation mode**: if `<delegation>` tag is present, receive Gemini/Codex output and render only the final judgment — do not reprocess

## Cost Doctrine

This skill is designed around the principle that **Claude is expensive**. The architecture ensures Claude only handles what cannot be delegated:

```
Task
 └── Scripts (free)
      └── Gemini (cheap, bulk)
           └── Codex (mid, code)
                └── Claude ← org-syntax renders here
```

The `<org_analysis>` tag is the handoff point. By the time Claude sees it, the analytical work is already done.

## Example

Given `<org_analysis>` context containing perspectives from Legal, Finance, and Operations:

```
【法務部】
契約条件の観点から、第3条に曖昧な表現が含まれており修正が必要です。特に「合理的な期間」の定義が不明確で、紛争リスクがあります。現行の約款との整合性も確認が必要です。

【財務部】
初期コストは許容範囲内ですが、3年目以降の維持費が過小評価されている可能性があります。ROI試算では損益分岐点が18ヶ月と算出されており、現金フローへの影響を要確認です。

【オペレーション部】
実装フェーズは現行チームのキャパシティで対応可能です。ただし4月〜6月の繁忙期と重なるため、開始タイミングの調整を推奨します。

---
各部署の指摘を踏まえると、契約修正と財務モデルの再精査を条件として進める方向が妥当です。特に法務の懸念点は着手前に解消必須。タイミングはQ3開始を推奨します。
```

## Setup

Install via Claude Code plugin system. Once installed, Claude will automatically apply this format whenever `<org_analysis>` appears in the conversation context.
