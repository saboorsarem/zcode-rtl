---
name: rtl
description: Apply or fix right-to-left (RTL) text and layout direction for Arabic, Hebrew, Persian, Urdu and other RTL scripts - in plain text, HTML, CSS, JavaScript UIs and documents. Use when text renders in the wrong order, punctuation lands on the wrong side, mixed Latin/RTL content breaks, or a layout must mirror for RTL locales.
---

# RTL (Right-to-Left) direction support

Make text and layouts display correctly for RTL scripts (Arabic, Hebrew, Persian, Urdu).

Core rule: never "fix" direction by physically reversing stored strings. Character order
in files, strings and databases stays logical (first typed character first); direction is
a presentation property applied at display time.

## 1. Pick the mechanism by target

- Plain text (chat, clipboard, .txt, source strings) -> Unicode bidi control marks (section 2).
- HTML -> `dir` attributes (section 3).
- CSS / styling -> `direction` + logical properties (section 4).
- App UI code (React/Vue/etc.) -> locale-driven `dir` (section 5).
- Word/DOCX -> paragraph RTL property (section 6).

## 2. Plain text: Unicode bidi control characters

Insert invisible control characters; they never appear as glyphs.

| Char | Code point | Use |
|------|------------|-----|
| RLM  | U+200F     | Strong RTL mark after neutral characters (`.`, `(`, `$`, digits) so they resolve RTL |
| LRM  | U+200E     | Strong LTR mark, symmetric case |
| RLI  | U+2067 (close PDI U+2069) | Isolate a run that must read RTL inside LTR surroundings |
| LRI  | U+2066 (close PDI U+2069) | Isolate a run that must read LTR (URLs, code, phones) inside RTL text |

Rules:

- A whole line/paragraph that must be RTL inside an LTR context: prefix with RLI (U+2067)
  and append PDI (U+2069). A single RLM prefix is a cheaper fix when only the trailing
  neutral punctuation misplaces.
- Latin words, URLs, code, e-mail addresses and phone numbers inside RTL text: wrap with
  LRI...PDI so they keep internal LTR order.
- Numbers stay LTR inside RTL text naturally; do not reverse them, but wrap groups like
  `100$` or version strings in LRI when an adjacent symbol flips.
- When producing the characters in a file, write them as the actual Unicode characters
  (or `\u200F` / `\u2067` escapes in code), never as visible placeholder text.

Example: the line `Price: 100$` written under an Arabic heading should become
`\u2067السعر: \u2066100$\u2069 (USD)\u2069` — the Arabic run isolated RTL, the amount isolated LTR.

## 3. HTML

- Whole page: `<html dir="rtl" lang="ar">` (lang: he/fa/ur as appropriate).
- Mixed pages: put `dir` on the smallest container that is entirely one direction.
- User-generated content of unknown language: `dir="auto"` on the block, `<bdi>` for
  inline fragments (usernames, titles).
- Never mirror content with `transform: scaleX(-1)` on text containers: it breaks text
  selection, copy-paste and screen readers. Mirroring is for icons/arrows only.

## 4. CSS

- Container: `direction: rtl;` (inherited). For content in unknown/mixed language:
  `unicode-bidi: plaintext;`.
- Replace physical properties with logical ones so one stylesheet serves both directions:
  - `margin-left` / `margin-right` -> `margin-inline-start` / `margin-inline-end`
  - `padding-left` / `padding-right` -> `padding-inline-start` / `padding-inline-end`
  - `border-left` -> `border-inline-start`; `border-radius: 0 4px 4px 0` -> corner-logical or `[dir="rtl"]` override
  - `left` / `right` offsets -> `inset-inline-start` / `inset-inline-end`
  - `text-align: left` / `right` -> `text-align: start` / `end`
  - `float: left` -> `float: inline-start`
- Directional overrides scope with `[dir="rtl"]` (or `:dir(rtl)`), e.g. chevrons:
  `[dir="rtl"] .icon-chevron { transform: scaleX(-1); }`.
- Audit shadows, gradients, progress bars, carousels and keyframe animations: their
  direction must follow `start/end` or `[dir="rtl"]` too, or they will animate the wrong way.

## 5. App UI code

- Set `document.documentElement.dir` (or root component `dir`) from the active locale:
  ar, he, fa, ur and similar -> rtl; everything else -> ltr.
- Derive direction from locale data (Intl / i18n library), not hardcoded per component.
- Forbid hardcoded `left/right` spacing classes in components; use the logical utilities
  the framework provides.

## 6. Documents (DOCX)

- Paragraphs: RTL paragraph direction + right alignment. In OOXML: `<w:bidi/>` in the
  paragraph properties.
- Tables: `<w:bidiVisual/>` in table properties so columns run right-to-left.

## 7. Verify

- Preview the result and confirm: the line starts at the right edge, sentence order is
  correct, trailing punctuation sits at the left edge, embedded Latin/numbers still read LTR.
- Mirror-check the layout: sidebar switches side, progress fills right-to-left, chevrons
  point the other way, form labels align to the right.
- Test with at least one Arabic and one Hebrew sample string before declaring done.
