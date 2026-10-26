# Changelog

All notable changes to this project are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [0.1.0] - 2026-10-27

### Added

- `rtl` skill: right-to-left text and layout direction support for Arabic, Hebrew,
  Persian and Urdu — Unicode bidi control marks for plain text, `dir` attributes for
  HTML, `direction` + logical properties for CSS, locale-driven `dir` for UI code,
  `<w:bidi/>` for DOCX paragraphs, and a verification checklist.
- `/rtl` command: apply RTL direction to a given text, file, or layout in one step.
- `README.ar.md`: Arabic translation rendered true right-to-left (`dir="rtl"`), with a
  language switcher in the English README.
