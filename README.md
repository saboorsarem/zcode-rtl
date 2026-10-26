<div align="center">

# RTL Direction

**Right-to-left text and layout support for [ZCode](https://z.ai) — for Arabic, Hebrew, Persian, Urdu and every other RTL script.**

[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)
[![Platform: ZCode](https://img.shields.io/badge/Platform-ZCode-blueviolet)](https://z.ai)
[![Version](https://img.shields.io/badge/Version-0.1.0-orange)](CHANGELOG.md)

*One skill, one command — and mixed-direction text stops breaking.*

</div>

---

## Why

Paste `السعر: 100$` into a left-to-right chat or codebase and the pieces jump around:
the colon lands on the wrong side, the `$` flips across the number, embedded Latin
words scatter, and whole layouts mirror incorrectly.

The common "fix" — physically reversing the string — corrupts it forever: search,
copy-paste, screen readers and databases all break. **Direction is a presentation
property, not a character-order property.** This plugin teaches your ZCode agent to
apply it correctly, per medium.

## What it does

| Target | Mechanism the agent applies |
|---|---|
| Plain text (chat, strings, .txt) | Invisible Unicode bidi control marks: `RLI`/`LRI` isolates, `RLM`/`LRM` |
| HTML | `dir="rtl"` / `dir="auto"` / `<bdi>` on the right containers |
| CSS | `direction: rtl` + **logical properties** (`margin-inline-start` instead of `margin-left`, …) |
| App UI code (React/Vue/…) | `dir` derived from the active locale (ar, he, fa, ur → rtl) |
| Word / DOCX | `<w:bidi/>` paragraph direction, `<w:bidiVisual/>` tables |

It also audits the easy-to-miss parts: directional icons and chevrons, shadows,
gradients, progress bars, animations, trailing punctuation, and mixed Latin/number
runs inside RTL text.

## Installation

### Method 1 — add this repository as a plugin marketplace (recommended)

1. Open **Plugin Marketplace → Add → Add Plugin Marketplace**.
2. Paste the repository URL:

   ```
   https://github.com/saboorsarem/zcode-rtl
   ```

3. Open **Personal → saboorsarem-zcode-plugins → RTL Direction → Install**.
4. Manage it any time under **Settings → Plugins**.

> If your build does not accept a GitHub URL as a market source, clone the repo and
> add the local folder instead — see Method 2.

### Method 2 — local folder

```bash
git clone https://github.com/saboorsarem/zcode-rtl.git
```

Then **Plugin Marketplace → Add → Add Plugin Marketplace** and paste the path to the
cloned `zcode-rtl` folder (it contains `.claude-plugin/marketplace.json`).

## Usage

**Slash command** — apply RTL to anything in one step:

```
/rtl Make this page right-to-left for Arabic
/rtl السعر: 100$
/rtl this components/pricing-card.tsx file
```

**Skill (automatic)** — just ask; the agent picks the mechanism by target:

```
Make this invoice line display correctly in the chat: الفاتورة: 2500 ريال
Add Arabic support to this landing page and mirror the layout
```

## Example

Input typed into an LTR chat:

```
السعر: 100$
```

The agent returns the same characters, wrapped in a right-to-left isolate:

```
⁧السعر: 100$⁩
```

Byte-exact: `U+2067` (RLI) before, `U+2069` (PDI) after. The isolate makes the whole
line lay out as one RTL unit in *any* host — the price sits at the right edge, the
colon follows it leftward, and `100$` still reads left-to-right. Nothing inside the
string was reversed, so search and copy keep working.

## Bidi control character reference

The agent uses these invisible characters — they never appear as glyphs:

| Char | Code point | Use |
|------|------------|-----|
| RLM  | `U+200F` | Strong RTL mark after neutral characters (`.`, `(`, `$`) so they resolve RTL |
| LRM  | `U+200E` | Strong LTR mark, symmetric case |
| RLI  | `U+2067` (+ `U+2069` PDI) | Isolate a run that must read RTL inside LTR surroundings |
| LRI  | `U+2066` (+ `U+2069` PDI) | Isolate a run that must read LTR (URLs, code, phones) inside RTL text |

## Repository layout

```
zcode-rtl/
├── .claude-plugin/
│   └── marketplace.json    ← marketplace catalog (this repo = one-plugin market)
├── rtl/                    ← plugin source
│   ├── .zcode-plugin/
│   │   └── plugin.json     ← ZCode plugin manifest
│   ├── skills/
│   │   └── rtl/SKILL.md    ← the direction-fixing skill
│   └── commands/
│       └── rtl.md          ← the /rtl slash command
├── CHANGELOG.md
├── LICENSE
└── README.md
```

## Compatibility

Built for ZCode's plugin format (`.zcode-plugin/plugin.json`), which follows the
Claude-Code-style plugin/marketplace conventions. The skill contains no runtime
dependencies — everything is applied by the agent itself.

## Contributing

Issues and pull requests are welcome — especially RTL test samples in less common
scripts (Pashto, Kurdish Sorani, Sindhi, Yiddish, Divehi…).

## License

[MIT](LICENSE) © [Saboor Sarem](https://github.com/saboorsarem)
