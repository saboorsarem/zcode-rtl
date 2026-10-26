---
description: Apply right-to-left direction to text, a file or a layout
---

Apply RTL (right-to-left) direction to the target below.

Target: $ARGUMENTS

Steps:

1. If the target is empty, ask what to convert and stop.
2. Determine the target kind: raw text, a file, an HTML page, CSS, app UI code, or a document.
3. Follow the plugin's rtl skill: Unicode bidi control marks for plain text, `dir` attributes
   for HTML, `direction` + logical properties for CSS, locale-driven `dir` for UI code,
   `<w:bidi/>` for DOCX paragraphs.
4. Never reverse stored strings; direction is presentation only. Keep identifiers and output
   text in English unless the file already uses another language.
5. Apply the change, then show the result with a one-sentence explanation and how to verify
   it (open/preview the file, or the exact characters inserted for plain text).
