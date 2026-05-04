# MathNote — Quick Reference (macOS)

A document-based editor for `.md` and `.tex` files with a live
KaTeX + marked.js preview. Single window, split into editor (left)
and preview (right).

## Toolbar

| Icon | What it does |
|---|---|
| ⟲ (circle arrow) | Manual refresh — re-render the preview now. **⌘R**. |
| ⟳ (two arrows) | **Auto-refresh** toggle. When on, preview updates 200 ms after each edit. |
| 🎓 graduation cap | **Blackboard mode** — slate-green chalkboard theme with chalk text. |
| *ƒ(x)* | **KaTeX** toggle — off renders math as plain text for debugging. |
| ⬛⬜ split picker | **View mode** — Editor / Split / Preview. |
| 1·2·3 (list) | **Theorem sidebar** — every theorem-like environment in the document; click a row to jump the editor there. |
| ⚠ | **Diagnostics** — open the panel. Shows a red/orange badge with the error count; each row lists the source line when the engine knows it. |
| ⇆ (branch) | **Diff panel** — line-by-line comparison vs the on-disk file. Click a hunk's *Line N* to jump the editor caret there. |
| ↗ | **Open Externally** — launch this file in your chosen external editor (TeXstudio, VS Code, BBEdit, …). First click prompts you to pick (separately for `.md` and `.tex`); hold ⌥ on subsequent clicks to switch apps. |
| ?  | **Quick Reference** — this page. |
| Markdown / LaTeX | File format badge. |

## Keyboard

| Shortcut | Action |
|---|---|
| ⌘S | Save |
| ⌘R | Refresh preview |
| ⌘F | Find in editor |
| ⌥⌘F | Find & Replace in editor |
| ⌘G / ⇧⌘G | Find Next / Previous |
| ⌘E | Use Selection for Find |
| ⌘J | Jump to Selection |
| ⇧⌘F | Find in preview |
| ⌥⌘E | Export as PDF… (also File ▸ Export as PDF…) |
| ⌥⌘O | Open in External Editor |
| ⇧⌘D | Show / hide Diff panel |
| ⇧⌘I | Insert ▸ Image… |
| ⇧⌘A | Insert ▸ File Attachment… |
| ⇧⌘L / ⇧⌘R | Insert ▸ Rotate Image Left / Right |
| ⌘, | Settings |
| ⌘? | Quick Reference (this page) |
| `trig/` | Expand snippet whose trigger is `trig` |

## Editor

- **Auto-close**: typing `(`, `[`, `{`, `$`, `\{` auto-inserts the matching closer; typing the closer over the auto-inserted one is a no-op ("typed-through").
- **`\begin{env}`**: after you finish typing `\begin{envname}`, a matching `\end{envname}` is inserted on the next line.
- **Snippets**: `trig/` expands to whatever the matching row in Settings ▸ Snippets defines (≈ 20 defaults shipped).
- **⌘-click jump-to-definition** on `\ref{key}`, `\eqref{key}`, `\cref{key}`, `\Cref{key}`, `\autoref{key}`, or `\label{key}` jumps to where that label was defined.
- **Diff gutter**: lines changed since last save get a colored mark in the gutter; the full per-line diff lives in the Diff panel.
- **Line numbers** (optional): enable via Settings ▸ Fonts ▸ Editor ▸ Show Line Numbers.
- **Syntax highlighting**: TeXstudio-like theme, fully customisable in Settings ▸ Colors.

## Preview

- **Inline math**: `$…$` or `\(…\)`. **Display math**: `$$…$$`, `\[…\]`, or `\begin{equation|align|…}…\end{…}`.
- **Theorem-like environments** (`\begin{theorem}…\end{theorem}` and the full family: lemma, proposition, corollary, definition, example, remark, proof, conjecture, claim, observation, assumption, exercise, fact, note, problem, question, solution) render as `**Env.** body` in both `.md` and `.tex`. `proof` also gets a `$\square$` at the end.
- **Images**: `\includegraphics[width=0.7\textwidth]{…}` renders inline. Markdown `![](…)` and raw `<img src="…">` work too; paths resolve relative to the document.
- **Links and attachments**: `[name](file.pdf)` in markdown or `\href{file.pdf}{name}` in LaTeX renders as a clickable link; activating it opens the target in its default app (PDFs in Preview, spreadsheets in Numbers, …).
- **LaTeX-only extras** (on `.tex` files): `\textbf`/`\textit`, `\section*` family, `tabular`/`tabular*` → GitHub-flavored markdown tables, `itemize`/`enumerate` → markdown lists, `\begin{comment}…\end{comment}` is stripped entirely so drafts stay hidden.
- **Macros**: `\newcommand` / `\def` / `\DeclareMathOperator` / `\DeclarePairedDelimiter` parsed from the document's own preamble are passed to KaTeX — no reload needed.
- **Global preamble**: Settings ▸ Macros — define inline or import a `.tex` file whose macros apply to every document.
- **Double-click a preview block** to jump the editor caret to its source line. (Scrolling the preview does *not* scroll the editor — preview-to-editor sync is double-click-only.)
- **Editor → Preview scroll sync**: editor scroll drives the preview, so moving through the source keeps the preview pinned to the same block.

### Find in Preview (⇧⌘F)

| Key | Action |
|---|---|
| Return | Next match |
| ⇧Return | Previous match |
| ⎋ (Esc) | Close find bar |

Matches are highlighted yellow; the current match is highlighted orange and scrolled into view. Find skips KaTeX-rendered math spans so queries don't break rendered equations.

## Insert menu

- **Image…** (⇧⌘I) — pick an image, copied into `{docname}.assets/` sidecar, inserted as `<img>` / `\includegraphics` with 70%-width default (editable).
- **File Attachment…** (⇧⌘A) — any file type. Copied into the same `.assets/` folder, inserted as `[name](path)` / `\href{path}{name}`. Click the link in the preview to open in the default app.
- **Rotate Image Left / Right** (⇧⌘L / ⇧⌘R) — operates on the image referenced on the editor's current line; rewrites the file on disk so the rotation persists through exports and LaTeX compilation.

## File menu

- **Save** (⌘S)
- **Export as PDF…** (⌥⌘E) — multi-page paginated A4 with 1" margins; always rendered in the light theme regardless of the current blackboard setting.
- **Open in External Editor** (⌥⌘O) — launch the current file in your preferred editor (per-format: `.md` and `.tex` have independent slots; ⌥ while invoking to swap).

## Settings (⌘,)

Five tabs:

- **Macros** — define user macros inline (`\name → \mathbb{R}`) or import a `.tex` preamble file. Applied to every document; passed to KaTeX and recognized by the syntax highlighter.
- **Snippets** — trigger/expansion pairs, ≈ 20 defaults shipped. Use `$0` in the expansion to mark the cursor landing position.
- **Colors** — TeXstudio-like syntax theme for the editor.
- **Fonts** — editor & preview sizes (10–24 pt), **Preview Font Family** (normal view), **Blackboard Font Family** (blackboard view), **Editor ▸ Show Line Numbers** toggle.
- **Editors** — per-format external editor preference: a separate app can be set for `.md` (VS Code, Typora, Obsidian, …) and `.tex` (TeXstudio, TeXShop, …). File ▸ Open in External Editor dispatches based on the document's format.

## File formats

- **`.md`** — plain Markdown with KaTeX math and the full theorem-env family.
- **`.tex`** — full LaTeX source. Envelope (`\documentclass`, `\begin{document}`) auto-stripped; local `\newcommand` / `\DeclareMathOperator` / `\DeclarePairedDelimiter` applied; `\begin{comment}…\end{comment}` blocks hidden.
- **PDF** — File ▸ Export as PDF…, paginated to A4 with 1" margins, light theme.

## Gotchas

- `\$100.02` → literal dollar (odd backslash count = escaped). `\\$x$` → `\\` line-break followed by math opener (even backslash count = accepted).
- `\[…\]` **outside** math mode → display math. **Inside** math mode → literal brackets (via a default KaTeX macro).
- Image and file-attachment insertion require the document to have been saved at least once — the sidecar folder sits next to the document.
- KaTeX handles most LaTeX math; untranslated commands and parse errors land in the Diagnostics panel.

*For the long-form manual see Help ▸ MathNote Manual; for the marketing intro see `docs/Introduction.md`.*
