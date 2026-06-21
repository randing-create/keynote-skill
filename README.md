# 🎤 Keynote Skill for Claude

A Claude skill that turns your talk outline into a **polished, browser-ready presentation** — complete with speaker notes, audience interaction moments, and a companion countdown timer.

---

## ✨ Highlights

- **Form-first workflow** — fill in a structured form (title, audience, story, quotes, CTA), click submit, and Claude builds the full deck
- **Zero setup** — output is a single `index.html` that opens in any browser, no build tools needed
- **Editorial design system** — warm sand palette, Noto Serif SC + EB Garamond typography, ghost characters, grain texture
- **Fragment reveals** — interactive moments (💬 polls, ✍️ reflections, ❤️ reactions) click in one by one, never auto-fire
- **Speaker notes** — every slide gets `⏱ timing / 目标 / 重点 / ✗禁忌 / 金句⭐ / 转场语`, press **S** to open speaker view
- **Companion timer** — a separate `timer.html` with per-act countdown, warning colors, pause/resume
- **Bilingual** — Chinese, English, or mixed; defaults to Noto Serif SC for CJK text

---

## 📦 Install

1. Download [`keynote.skill`](./keynote.skill)
2. Open **Claude Cowork** → Settings → Capabilities → Skills
3. Click **Save skill** on the downloaded file

---

## 🚀 Use

In any new conversation, say:

> 我想准备一个演讲  
> Help me build a keynote  
> 帮我做TED演讲稿

Claude will show the input form. Fill in your talk details, click **生成演讲稿幻灯片**, and Claude will generate:

- `index.html` — the full Reveal.js presentation (open in browser, press `S` for speaker view)
- `timer.html` — per-act countdown timer (open on your phone or second screen)

---

## 🎨 Design System

| Token | Value | Use |
|---|---|---|
| `--sand` | `#F0E6D6` | Background |
| `--umber` | `#2A2520` | Primary text |
| `--gold` | `#C08A28` | Key quotes, highlights |
| `--terra` | `#B46E46` | Emotional accents |
| `--moss` | `#5E6E52` | Micro prompts, supporting |

Fonts: **Noto Serif SC** · **EB Garamond** · **Space Mono** (all via Google Fonts CDN)

---

## 📁 Files

```
keynote-skill/
└── SKILL.md        ← skill instructions (what Claude reads)
keynote.skill       ← installable package
```

---

Made with [Claude Cowork](https://claude.ai) · Built by [@randing](https://github.com/randing)
