---
name: keynote
description: Create polished keynote / TED-style presentations as a single self-contained HTML file using Reveal.js. Use this skill whenever the user wants to build a presentation, speech deck, slide deck, or keynote — especially story-driven talks with a personal narrative arc, timed speaker notes, and interactive audience moments. Triggers on: "make slides for my talk", "turn my outline into a presentation", "I'm giving a speech", "TED talk deck", "keynote for X audience", "presentation with speaker notes", "create a deck", "演讲", "演示文稿", "幻灯片", "TED演讲". Also use this skill proactively when the user has been working on speech content, scripts, or talk outlines and hasn't yet converted them to slides — even if they don't say "presentation."
---

# Keynote / TED Talk Skill

Build a self-contained Reveal.js presentation as a single `index.html` — no build step, no external CSS/JS except CDN links. Deliverables: `index.html` (slides) + `timer.html` (companion countdown).

---

## Step 0: Show the Input Form First

**Before doing anything else**, use `show_widget` to display the input collection form below. This lets the user fill in their talk details in a structured way. The form's submit button calls `sendPrompt()` which sends the filled-in data back to you — at that point, proceed with Steps 1–10 to build the presentation.

**Skip this step only if** the user has already provided all key inputs inline (title, audience, duration, core message, story content). If they've pasted a full script, go straight to Step 1.

### Input Form HTML (copy exactly into show_widget)

```html
<h2 class="sr-only">演讲稿定制表单 — 填写完成后自动生成 Reveal.js 幻灯片</h2>
<style>
.kn-form { padding: 1.5rem 0 2rem; max-width: 620px; }
.kn-section { margin-bottom: 1.75rem; }
.kn-label { display: block; font-size: 13px; font-weight: 500; color: var(--color-text-secondary); margin-bottom: 6px; letter-spacing: .03em; }
.kn-hint { font-size: 12px; color: var(--color-text-tertiary); margin-top: 4px; }
.kn-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 16px; }
.kn-tag-row { display: flex; flex-wrap: wrap; gap: 8px; margin-top: 6px; }
.kn-tag { font-size: 13px; padding: 5px 14px; border-radius: 999px; border: 0.5px solid var(--color-border-secondary); background: var(--color-background-primary); color: var(--color-text-secondary); cursor: pointer; transition: all .15s; user-select: none; }
.kn-tag.active { background: var(--color-background-secondary); border-color: var(--color-border-primary); color: var(--color-text-primary); font-weight: 500; }
.kn-submit { width: 100%; padding: 12px; font-size: 15px; font-weight: 500; margin-top: 0.5rem; display: flex; align-items: center; justify-content: center; gap: 8px; }
.kn-divider { border: none; border-top: 0.5px solid var(--color-border-tertiary); margin: 1.75rem 0; }
textarea { resize: vertical; min-height: 80px; }
</style>
<div class="kn-form">
  <div class="kn-section kn-grid">
    <div>
      <label class="kn-label" for="kn-title">演讲标题 / Talk title</label>
      <input type="text" id="kn-title" placeholder="AI时代，什么能力更重要？">
    </div>
    <div>
      <label class="kn-label" for="kn-duration">时长（分钟）</label>
      <input type="number" id="kn-duration" placeholder="60" min="5" max="180">
    </div>
  </div>
  <div class="kn-section">
    <label class="kn-label">演讲语言</label>
    <div class="kn-tag-row" id="kn-lang-tags">
      <div class="kn-tag active" data-val="中文">中文</div>
      <div class="kn-tag" data-val="英文">English</div>
      <div class="kn-tag" data-val="中英双语">中英双语</div>
    </div>
  </div>
  <div class="kn-section">
    <label class="kn-label" for="kn-audience">目标听众</label>
    <input type="text" id="kn-audience" placeholder="例：人力资源从业者，关注员工发展">
    <p class="kn-hint">他们是谁？他们相信什么？他们需要感受到什么？</p>
  </div>
  <hr class="kn-divider">
  <div class="kn-section">
    <label class="kn-label" for="kn-message">核心信息 — 你最想让观众带走的一句话</label>
    <input type="text" id="kn-message" placeholder="例：成绩可以被替代，但学习欲望不会">
  </div>
  <div class="kn-section">
    <label class="kn-label" for="kn-story">故事/经历脉络</label>
    <textarea id="kn-story" placeholder="你经历过什么？关键转折点在哪里？可以贴演讲稿，也可以简单描述几个关键时刻……"></textarea>
  </div>
  <div class="kn-section">
    <label class="kn-label" for="kn-quotes">金句（可选）</label>
    <textarea id="kn-quotes" placeholder="值得被拍照的那句话，一行一条" style="min-height:60px;"></textarea>
  </div>
  <div class="kn-section">
    <label class="kn-label" for="kn-interaction">互动设计（可选）</label>
    <input type="text" id="kn-interaction" placeholder="例：开场投票、弹幕提问、小组讨论">
  </div>
  <div class="kn-section">
    <label class="kn-label" for="kn-cta">行动号召 — 观众听完后你希望他们做什么</label>
    <input type="text" id="kn-cta" placeholder="例：扫码加入社群 / 申请1v1辅导">
  </div>
  <div class="kn-section">
    <label class="kn-label" for="kn-extra">其他备注（可选）</label>
    <textarea id="kn-extra" placeholder="主视觉风格偏好、特殊幻灯片需求、不想提的话题……" style="min-height:60px;"></textarea>
  </div>
  <button class="kn-submit" onclick="submitKeynote()">
    <i class="ti ti-sparkles" aria-hidden="true"></i>
    生成演讲稿幻灯片 ↗
  </button>
</div>
<script>
document.querySelectorAll('#kn-lang-tags .kn-tag').forEach(tag => {
  tag.addEventListener('click', () => {
    document.querySelectorAll('#kn-lang-tags .kn-tag').forEach(t => t.classList.remove('active'));
    tag.classList.add('active');
  });
});
function v(id) { const el = document.getElementById(id); return el ? el.value.trim() : ''; }
function submitKeynote() {
  const title = v('kn-title'), duration = v('kn-duration'), audience = v('kn-audience'),
        message = v('kn-message'), story = v('kn-story'), quotes = v('kn-quotes'),
        interact = v('kn-interaction'), cta = v('kn-cta'), extra = v('kn-extra');
  const lang = document.querySelector('#kn-lang-tags .kn-tag.active')?.dataset.val || '中文';
  if (!title || !audience || !message || !story) { alert('请至少填写：标题、听众、核心信息、故事脉络'); return; }
  let p = `请用 keynote skill 帮我生成完整的演讲幻灯片（index.html）和计时器（timer.html）。\n\n`;
  p += `**演讲标题：** ${title}\n`;
  if (duration) p += `**时长：** ${duration} 分钟\n`;
  p += `**语言：** ${lang}\n**目标听众：** ${audience}\n**核心信息：** ${message}\n`;
  p += `\n**故事/内容脉络：**\n${story}\n`;
  if (quotes) p += `\n**金句：**\n${quotes}\n`;
  if (interact) p += `\n**互动设计：** ${interact}\n`;
  if (cta) p += `\n**行动号召：** ${cta}\n`;
  if (extra) p += `\n**备注：** ${extra}\n`;
  sendPrompt(p);
}
</script>
```

After the user submits the form and the structured prompt arrives, proceed directly to Steps 1–10 below.

---

## Step 1: Gather Before Building

Extract from the conversation, or ask for:

- **Title & tagline** — the big question or statement, plus a subtitle
- **Audience** — who are they, what do they already believe, what do they need to feel?
- **Story arc** — key acts and emotional beats (not just bullet points)
- **Duration** — total talk length (use this to set slide count and timer segments)
- **Key quotes** — lines that deserve to be photographed
- **Interactive moments** — audience questions, polls, reflection pauses
- **CTA** — what one thing should the audience do after?
- **Bilingual needs** — does the talk mix languages? (common in Chinese keynotes)

If the user provides a script or outline, extract all of this from it rather than asking redundant questions.

**Slide count rule of thumb:**
| Duration | Slides | Pace |
|---|---|---|
| 20 min | 8–10 | ~2 min/slide |
| 40 min | 10–14 | ~3 min/slide |
| 60–70 min | 12–18 | ~3–4 min/slide |

Don't pad. Every slide should earn its place.

---

## Step 2: Design System

Use this palette and type system. It creates a warm, editorial, trustworthy aesthetic — readable in any conference room.

### Colors (CSS variables)

```css
:root {
  --sand:  #F0E6D6;   /* background */
  --umber: #2A2520;   /* primary text */
  --gold:  #C08A28;   /* key quotes, highlights */
  --terra: #B46E46;   /* emotional accents, secondary */
  --moss:  #5E6E52;   /* micro prompts, supporting */
}
```

### Typography

Load from Google Fonts:
```html
<link href="https://fonts.googleapis.com/css2?family=Noto+Serif+SC:wght@200;300;400;500;700&family=EB+Garamond:ital,wght@0,400;1,400;1,500&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
```

| Class | Font | Use |
|---|---|---|
| `.t-hero` | Noto Serif SC 700 | Big slide statements, 56–108px |
| `.t-large` | Noto Serif SC 500 | Supporting points, 36–68px |
| `.t-mid` | Noto Serif SC 300 | Context lines, 26–48px |
| `.t-gara` | EB Garamond italic | Quotes, captions, 17–25px |
| `.t-mono` | Space Mono | Labels, timestamps, eyebrows, 11–14px |

For Chinese text, prefer Noto Serif SC. For English within a Chinese presentation, use EB Garamond italic for visual contrast.

### Background Grain

A subtle noise texture lifts the design out of flat-digital:

```css
#grain {
  position: fixed; inset: 0; z-index: 500;
  pointer-events: none; opacity: .042;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='300' height='300'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.75' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='300' height='300' filter='url(%23n)' opacity='1'/%3E%3C/svg%3E");
}
```

```html
<div id="grain"></div>
```

---

## Step 3: Reveal.js Setup

```html
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/4.6.0/reveal.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/reveal.js/4.6.0/reveal.min.js"></script>
```

Initialize with:
```js
Reveal.initialize({
  hash: true, history: true,
  transition: 'fade', transitionSpeed: 'slow',
  backgroundTransition: 'fade',
  progress: true, slideNumber: 'c/t',
  controls: true, center: false,
  width: 1440, height: 810,
  margin: 0, minScale: .15, maxScale: 2.0,
});
```

Each slide: `<section data-transition="fade" data-transition-speed="slow">`

Press **S** to open speaker view (notes + preview + timer).

---

## Step 4: Slide Shell & Layout

Every slide uses this wrapper:

```html
<section data-transition="fade" data-transition-speed="slow">
  <div class="s">
    <!-- ghost background character — pick one per slide -->
    <div class="ghost" style="font-size:480px; right:-80px; top:50%; transform:translateY(-52%);">选</div>

    <!-- content with entry animation classes -->
    <div class="t-mono ai">EYEBROW LABEL</div>
    <div class="t-hero ai2">Main Statement</div>

    <aside class="notes">...</aside>
  </div>
</section>
```

### The .s container

```css
.s {
  width: 100%; height: 100%;
  padding: 68px 92px;
  display: flex !important;
  flex-direction: column;
  justify-content: center;
  position: relative;
  overflow: hidden;
}
```

### Ghost Characters

Each slide has one large character in the background — nearly invisible, adds depth. Choose a character that reflects the slide's theme.

```css
.ghost {
  position: absolute;
  font-family: 'Noto Serif SC', serif;
  font-weight: 700;
  color: var(--umber);
  opacity: .034;
  pointer-events: none;
  user-select: none;
}
```

---

## Step 5: Entry Animations

```css
@keyframes fadeUp {
  from { opacity: 0; transform: translateY(22px); }
  to   { opacity: 1; transform: translateY(0); }
}
@keyframes fadeIn {
  from { opacity: 0; }
  to   { opacity: 1; }
}
.ai  { animation: fadeUp .85s cubic-bezier(.16,1,.3,1) both; }
.ai2 { animation: fadeUp .85s cubic-bezier(.16,1,.3,1) both; animation-delay: .22s; }
.ai3 { animation: fadeUp .85s cubic-bezier(.16,1,.3,1) both; animation-delay: .42s; }
.ai4 { animation: fadeUp .85s cubic-bezier(.16,1,.3,1) both; animation-delay: .60s; }
.ai5 { animation: fadeUp .85s cubic-bezier(.16,1,.3,1) both; animation-delay: .78s; }
.ai6 { animation: fadeIn  1.0s ease both;                     animation-delay: .95s; }
```

Reset on slide change so animations replay every time:

```js
function resetAnims(slide) {
  slide.querySelectorAll('.ai,.ai2,.ai3,.ai4,.ai5,.ai6').forEach(el => {
    el.style.animation = 'none'; el.style.opacity = '0';
    void el.offsetHeight;
    el.style.animation = ''; el.style.opacity = '';
  });
}
Reveal.on('slidechanged', e => resetAnims(e.currentSlide));
Reveal.on('ready',        e => resetAnims(e.currentSlide));
```

---

## Step 6: Key Slide Patterns

### Title Slide

```html
<div class="t-mono ai" style="letter-spacing:.12em; color:rgba(42,37,32,.45); margin-bottom:20px;">
  EVENT · CITY · DATE
</div>
<div class="ai2" style="width:56px; height:2px; background:var(--gold); margin-bottom:32px;"></div>
<div class="t-hero ai3" style="line-height:1.15; max-width:900px;">Your Big Opening Question</div>
<div class="t-gara ai4" style="margin-top:24px; color:rgba(42,37,32,.6);">Subtitle or framing line</div>
```

### Narrative / Story Slide

```html
<div class="t-mono ai" style="color:rgba(42,37,32,.45); letter-spacing:.12em; margin-bottom:16px;">ACT 1 · 00:05</div>
<div class="t-large ai2" style="max-width:860px; line-height:1.4;">The core thing you're saying</div>
<div class="ai3" style="margin-top:32px; border-left:2px solid var(--gold); padding-left:24px; max-width:640px;">
  <div class="t-gara" style="color:var(--gold); font-size:22px; line-height:1.6;">"The quotable line"</div>
</div>
```

### Tension / Build Slide (Click-to-Reveal)

```html
<div class="t-large ai">Opening frame</div>
<div class="fragment fade-up" data-fragment-index="1" style="margin-top:28px;">
  <div class="t-mid">First revelation</div>
</div>
<div class="fragment fade-up" data-fragment-index="2" style="margin-top:16px;">
  <div class="t-large" style="color:var(--gold);">The punchline</div>
</div>
```

### Method / Framework Slide

```html
<div class="t-mono ai" style="color:rgba(42,37,32,.45); letter-spacing:.12em; margin-bottom:32px;">THE METHOD</div>

<div class="fragment fade-up" data-fragment-index="1" style="display:flex; align-items:center; gap:20px; margin-bottom:20px;">
  <div style="width:40px; height:40px; border:1.5px solid var(--gold); border-radius:50%;
              display:flex; align-items:center; justify-content:center;
              font-family:'Space Mono',monospace; font-size:12px; color:var(--gold); flex-shrink:0;">01</div>
  <div class="t-large">Step name</div>
</div>
<!-- repeat for each step -->
```

### CTA / Closing Slide

```html
<!-- Color band at top -->
<div style="width:100vw; height:4px; background:linear-gradient(90deg,var(--gold),var(--terra),var(--moss));
            position:absolute; top:0; left:-92px;"></div>

<div class="t-hero ai" style="color:var(--gold);">Final line one</div>
<div class="t-hero ai2">Final line two</div>

<div class="t-mid ai3" style="margin-top:32px; color:rgba(42,37,32,.6); max-width:560px;">
  What you're inviting them into
</div>

<!-- QR code — replace YOUR_URL -->
<img class="ai4" src="https://api.qrserver.com/v1/create-qr-code/?size=160x160&color=2A2520&bgcolor=F0E6D6&data=YOUR_URL"
     style="width:120px; height:120px; margin-top:28px;">
```

---

## Step 7: Micro Prompts (Interactive Moments)

Micro prompts are audience interaction callouts. **Always** `fragment fade-up` — the speaker clicks to reveal them deliberately.

```html
<div class="micro-prompt fragment fade-up">
  <span class="mp-icon">💬</span>
  <div class="mp-text"><strong>A:</strong> Option one &nbsp;&nbsp; <strong>B:</strong> Option two</div>
</div>
```

```css
.micro-prompt {
  display: flex; align-items: flex-start; gap: 14px;
  border: 1px solid rgba(94,110,82,.28); border-radius: 10px;
  padding: 16px 20px; background: rgba(94,110,82,.06);
  max-width: 640px; margin-top: 28px;
}
.mp-icon { font-size: 22px; line-height: 1; flex-shrink: 0; margin-top: 2px; }
.mp-text { font-family: 'Noto Serif SC', serif; font-size: 17px; font-weight: 300;
           color: var(--umber); line-height: 1.65; }
```

**Icon guide:**
- 💬 Chat box / word cloud
- ✍️ Written reflection (give 30 seconds of silence)
- ❤️ Raise hand / emotional response
- 🤔 Thinking question — let it land

**Fragment order on any slide:** auto-animated content (`.ai`) → content fragments → micro prompts last.

---

## Step 8: Speaker Notes Format

```
⏱ MM:SS – MM:SS | 目标: [one-sentence goal for this slide]

[What you SAY, not what the slide shows — 3–5 lines written for the ear]

重点: [what to slow down on, what to emphasize]
✗ [common pitfall — what NOT to do or say]

互动: [if there's an interactive moment — exact instructions, wait time]

金句 ⭐⭐⭐⭐⭐:
"[the quotable line in full]"

→ [exact transition sentence to the next slide]
```

Star ratings reflect quotability: ⭐⭐⭐⭐⭐ means pause after delivery. Press **S** in-browser to open speaker view.

---

## Step 9: Brand Mark

```html
<div id="brand" style="position:fixed; top:24px; left:34px; z-index:300; pointer-events:none;
                        font-family:'Space Mono',monospace; font-size:11px; letter-spacing:.18em;
                        color:rgba(42,37,32,.28);">SPEAKER · TITLE</div>
```

---

## Step 10: Companion Timer (timer.html)

A mobile-friendly countdown with per-act segments.

```js
const ACTS = [
  { name: "Opening\nS1–S2",  mins: 5 },
  { name: "Act 1\nS3–S4",    mins: 8 },
  // match user's actual timing from speaker notes
  { name: "Q&A",             mins: 10 },
];
```

Required features:
- Large countdown center (MM:SS)
- Gold color at ≤2 min, red when overtime (show negative time)
- Tap center to pause/resume
- Prev/Next act buttons
- Total remaining in header
- Progress bar per act

Derive act lengths from the ⏱ timestamps in speaker notes to keep both files in sync.

---

## Narrative Principles

Good keynotes follow a story arc:

1. **Hook** — curiosity first, credentials later
2. **Shared truth** — something the audience already believes
3. **First crack** — the moment that truth broke for you
4. **Journey** — 2–3 specific moments, stakes rising
5. **Pivot** — the emotional center, the realization
6. **Method** — what you built or discovered; make it usable
7. **Mirror** — help the audience see themselves in it
8. **Call** — one specific, concrete invitation to act

Slides are anchors for the spoken word, not the script. The best slide makes no sense without the speaker.

---

## GitHub Pages Deployment

If the user wants to host: push `index.html` and `timer.html` to a GitHub repo root. Go to Settings → Pages → Source: main branch / root. The presentation lives at `username.github.io/repo-name` — shareable, no login needed.
