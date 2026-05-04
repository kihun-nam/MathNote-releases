# MathNote — User Manual (macOS)

A lightweight macOS editor for Markdown and LaTeX files with live
KaTeX-rendered math preview. This manual walks through every feature
the Quick Reference summarises.

---

## Table of Contents

1. [Overview](#overview)
2. [Installation](#installation)
3. [Interface](#interface)
4. [File Formats](#file-formats)
5. [The Preview Pipeline](#the-preview-pipeline)
6. [The Editor](#the-editor)
7. [Auto-Refresh](#auto-refresh)
8. [Blackboard Mode](#blackboard-mode)
9. [Snippets](#snippets)
10. [User Macros](#user-macros)
11. [Insert Menu](#insert-menu)
12. [External Editors](#external-editors)
13. [Scroll & Click Sync](#scroll--click-sync)
14. [Find & Replace](#find--replace)
15. [Diagnostics Panel](#diagnostics-panel)
16. [Theorem Sidebar](#theorem-sidebar)
17. [Diff Panel](#diff-panel)
18. [PDF Export](#pdf-export)
19. [Settings](#settings)
20. [Keyboard Shortcuts](#keyboard-shortcuts)
21. [Tips for LaTeX Authors](#tips-for-latex-authors)
22. [Known Limitations](#known-limitations)

---

## Overview

MathNote is a document-based SwiftUI app that edits Markdown (`.md`)
and LaTeX (`.tex`) files side by side with a live rendered preview.
Math is rendered by **KaTeX**, Markdown by **marked.js** — both bundled
locally, so **no network calls** are made and the app works fully
offline.

Unlike a full LaTeX compiler, MathNote doesn't produce DVI/PDF output
from your document directly; instead, it extracts math regions, hands
them to KaTeX, and stitches the result back into the rendered HTML.
This gives sub-100 ms preview updates for documents with hundreds of
equations.

Minimum macOS version: **14.0 (Sonoma)**.

---

## Installation

Drag `MathNote.app` from the disk image into `/Applications`. The app
is signed with a Developer ID certificate and notarized by Apple, so
Gatekeeper accepts it on first launch — no right-click "Open Anyway"
dance is required.

On first launch MathNote registers itself as a handler for `.md` and
`.tex` files, so double-clicking either format from Finder opens it
in MathNote (subject to whatever you've set as the system default).

---

## Interface

```
┌─────────────────────────────────────────────────────────┐
│  Toolbar: ⟲ Refresh · ⟳ Auto · 🎓 Blackboard · ƒ(x) ·   │
│           Layout · 1·2·3 · ⚠ · ⇆ · ↗ · ?  ·  Markdown   │
├────────────────────────────┬────────────────────────────┤
│                            │                            │
│         Editor             │          Preview           │
│                            │                            │
│  (syntax-highlighted       │  (live KaTeX + marked      │
│   source, optional ruler)  │   output, double-click     │
│                            │   to jump back)            │
│                            │                            │
├────────────────────────────┴────────────────────────────┤
│  Diagnostics panel (toggle from ⚠)                      │
└─────────────────────────────────────────────────────────┘
```

The Theorem sidebar (toggle: 1·2·3 button) and the Diff panel (toggle:
⇆ button or ⇧⌘D) attach to the right edge when active.

### Toolbar buttons

| Button | Action |
|--------|--------|
| ⟲ Refresh Preview (⌘R) | Re-render the preview now |
| ⟳ Auto-refresh | When on, preview updates 200 ms after each edit |
| 🎓 Blackboard mode | Slate-green chalkboard theme with chalk text |
| ƒ(x) KaTeX | Toggle KaTeX rendering on/off |
| Layout picker | Editor / Split / Preview |
| 1·2·3 (list) | Show / hide theorem sidebar |
| ⚠ Diagnostics | Show / hide the diagnostics panel (badge shows error count) |
| ⇆ Diff panel | Show / hide line-diff-vs-saved sidebar |
| ↗ Open Externally | Launch this file in an external editor (per-format choice; ⌥-click to re-pick) |
| ? Quick Reference | Open the in-app Quick Reference sheet |
| LaTeX/Markdown badge | Indicates how the preview is being rendered |

---

## File Formats

MathNote opens two UTI families:

- **`public.tex-source`** (`.tex`) — full LaTeX source. MathNote reads
  the preamble, extracts user macros, converts theorem-like
  environments, and passes math blocks to KaTeX.
- **`net.daringfireball.markdown`** (`.md`) — Markdown with `$…$` /
  `$$…$$` / `\(…\)` / `\[…\]` math. No preamble processing; whatever
  you write goes straight through marked.js + KaTeX.

A badge in the toolbar tells you which mode is active. You don't
choose — it's determined by the file extension when the document is
opened.

### What MathNote does to a `.tex` file

1. **Strip LaTeX comments** (`%` to end of line, preserving `\%`).
2. **Strip `\begin{comment}…\end{comment}` blocks** in a
   line-count-preserving way so source-line sync stays accurate.
3. **Strip the document envelope** — everything before
   `\begin{document}` is treated as preamble; everything after
   `\end{document}` is discarded.
4. **Source-line annotation** — every non-blank block gets a
   `<!--src:N-->` comment so the preview can sync back to the editor.
5. **Parse macros** from the preamble *and* the body:
   - `\newcommand{\name}{body}` (and `\renewcommand`)
   - `\def\name{body}`
   - `\DeclareMathOperator{\cmd}{text}` (and starred variant)
   - `\DeclarePairedDelimiter{\cmd}{\left}{\right}`
6. **Expand starred paired-delimiter forms** (`\norm*{x}` →
   `\left\lVert x \right\rVert`) before math extraction.
7. **Extract math** (`$…$`, `$$…$$`, `\(…\)`, `\[…\]`, and the usual
   AMS environments) into placeholders.
8. **Text-mode substitutions**: sectioning → markdown headings,
   theorem envs → styled blocks, `\ref`/`\cref` → `(label)`, `tabular`
   → markdown table, `thebibliography` → `## References`, etc.
9. **Re-splice** the math blocks back in, wrapped in `$$…$$` / `$…$`
   delimiters so the JavaScript pipeline can hand them to KaTeX.

---

## The Preview Pipeline

The preview WebView loads a single bundled `preview.html` containing:

- `katex.min.css` + `katex.min.js` (v0.16.x)
- `marked.min.js`
- Fonts from the KaTeX fonts directory

The rendered HTML is produced in five steps:

1. JavaScript **`extractMath`** replaces every math construct with a
   placeholder.
2. **`marked.parse`** runs on the placeholder-stitched text.
3. Each placeholder is replaced with
   `katex.renderToString(body, { displayMode })`.
4. **`applySourceLineAttributes`** walks the DOM, moves `<!--src:N-->`
   comments onto their adjacent element as a `data-src-line="N"`
   attribute, and removes the comments.
5. Any `scrollToSourceLine` / `scrollToFraction` call from Swift
   adjusts the viewport.

KaTeX errors (if any) become red `<span class="katex-error">`
elements and are reported back to the Swift diagnostics store via the
`diagnostics` message channel.

---

## The Editor

### Typing aids

- **Auto-close** — typing `(`, `[`, `{`, `$`, or `\{` inserts the
  matching closer with the caret in between. Typing the closer again
  is a no-op ("typed-through") so you can keep flowing without
  thinking about which characters you've already paid for.
- **`\begin{env}` auto-close** — finishing `\begin{envname}` on a line
  inserts a matching `\end{envname}` on the next line.
- **Snippets** — see [Snippets](#snippets).
- **⌘-click jump-to-definition** — ⌘-click on a `\ref{key}`,
  `\eqref{key}`, `\cref{key}`, `\Cref{key}`, `\autoref{key}`, or
  `\label{key}` to jump to where that label was defined.

### Diff gutter

Lines you've changed since the last save get a coloured mark in the
left gutter. The mark clears the moment you save. The full per-line
diff lives in the [Diff panel](#diff-panel).

### Line numbers

Off by default. Enable via Settings ▸ Fonts ▸ Editor ▸ Show Line
Numbers — a narrow ruler is drawn on the left edge with 1-based line
numbers.

### Syntax highlighting

MathNote highlights both math-mode tokens and text-mode structural
elements. All colours customisable in **Settings ▸ Colors**.

#### Math-mode tokens

| Token | Default colour | Example |
|-------|----------------|---------|
| Control sequence | Blue | `\alpha`, `\frac`, `\norm` |
| Number | Green | `42`, `3.14` |
| Operator | Magenta | `+`, `=`, `\leq`, `\cdot` |
| Super/subscript | Red (bold) | `^`, `_` |
| Brace | Brown | `{`, `}`, `[`, `]`, `\left`, `\right` |
| Comment | Gray (italic) | `% …` |

#### Text-mode structural tokens

| Token | Default colour | Matches |
|-------|----------------|---------|
| Sectioning (bold) | Deep blue | `\part{…}`, `\chapter{…}`, `\chapter*{…}`, `\section{…}`, `\section*{…}`, `\subsection{…}`, `\subsubsection{…}`, `\paragraph{…}`, `\subparagraph{…}` |
| Environment wrapper | Amber | `\begin{foo}`, `\end{foo}` |

---

## Auto-Refresh

MathNote ships with auto-refresh **off** by default. With it off, the
preview only updates when you press **⌘R** (or change a setting that
affects rendering). With it on, every edit kicks a 200 ms debouncer;
the preview re-renders 200 ms after the last keystroke in a burst.

Toggle from the toolbar (⟳ icon, second button from the left).
Turning it on also kicks an immediate refresh so the preview catches
up with edits made before you flipped it.

The keyboard-only ⌘R workflow stays useful when you're editing a
large file and don't want every keystroke to schedule a re-render.

---

## Blackboard Mode

A dark slate-green chalkboard theme for the preview, with chalky
off-white body and math text. Toggle from the 🎓 (graduation cap)
toolbar button.

The blackboard body font is independent of the normal-mode preview
font — flip into blackboard mode and the preview switches to the
**Blackboard Font Family** picker's choice (default Chalkduster);
flip back and your normal-mode font returns. Both pickers live in
**Settings ▸ Fonts**.

Available blackboard fonts (all ship with macOS):

- Chalkduster (default)
- Chalkboard SE
- Palatino
- Noteworthy
- Marker Felt
- Bradley Hand
- Comic Sans MS

Math always renders in KaTeX's bundled Computer Modern fonts
regardless of the blackboard setting; only the body and headings
follow the picker.

PDF export ignores the blackboard setting and always renders in the
light theme — see [PDF Export](#pdf-export).

---

## Snippets

Snippets are text-expansion shortcuts triggered by typing the trigger
followed by `/` (chosen because `/` is rare in LaTeX).

### Example

Type `ali/` anywhere in the editor. The four characters are replaced
with:

```latex
\begin{align*}
▮
\end{align*}
```

…and the cursor is placed at `▮`. Press `Return` and keep typing.

### Default snippets

| Trigger | Expansion |
|---------|-----------|
| `ali/` | `\begin{align*} … \end{align*}` |
| `aln/` | `\begin{align} … \end{align}` |
| `eq/` | `\begin{equation} … \end{equation}` |
| `eqs/` | `\begin{equation*} … \end{equation*}` |
| `gat/` | `\begin{gather} … \end{gather}` |
| `itm/` | `\begin{itemize} \item … \end{itemize}` |
| `enum/` | `\begin{enumerate} \item … \end{enumerate}` |
| `thm/` | `\begin{theorem} … \end{theorem}` |
| `lem/` | `\begin{lemma} … \end{lemma}` |
| `prop/` | `\begin{proposition} … \end{proposition}` |
| `cor/` | `\begin{corollary} … \end{corollary}` |
| `defn/` | `\begin{definition} … \end{definition}` |
| `rem/` | `\begin{remark} … \end{remark}` |
| `ex/` | `\begin{example} … \end{example}` |
| `pf/` | `\begin{proof} … \end{proof}` |
| `bmat/` | `\begin{bmatrix} … \end{bmatrix}` |
| `pmat/` | `\begin{pmatrix} … \end{pmatrix}` |
| `cases/` | `\begin{cases} … \end{cases}` |
| `frac/` | `\frac{ … }{}` |
| `sqrt/` | `\sqrt{ … }` |

### Defining your own

**Settings ▸ Snippets** provides:

- An **Add Snippet** form. Write `$0` in the expansion to mark where
  the cursor lands. If omitted, the cursor goes to the end.
- An editable list of every existing snippet (click the chevron on a
  row to expand and edit its expansion).
- **Restore Defaults** and **Clear All** buttons.

```
Trigger:    mybox
Expansion:  \fbox{\parbox{0.9\textwidth}{$0}}
```

Now `mybox/` expands to a boxed paragraph with the cursor inside.

### Trigger rules

- The trigger is matched greedily over `[A-Za-z0-9]+` immediately
  before the `/`.
- Any non-identifier character resets the trigger search — so
  `x+ali/` expands `ali`, not `x+ali`.
- `/` inside math (the division operator) is safe: if there's no
  trigger word before it, no expansion happens.

---

## User Macros

There are two ways to tell MathNote about a user macro:

### 1. In the source file

MathNote parses `\newcommand`, `\renewcommand`, `\def`,
`\DeclareMathOperator`, and `\DeclarePairedDelimiter` directly from
your `.tex` source — both the preamble and the document body. They're
passed to KaTeX as macros at render time and stripped from the
rendered text.

```latex
\newcommand{\R}{\mathbb{R}}
\DeclareMathOperator{\Var}{Var}
\DeclarePairedDelimiter{\norm}{\lVert}{\rVert}
```

In the preview, `\R`, `\Var(X)`, and `\norm{x}` / `\norm*{x}` all
render correctly.

### 2. Globally via Settings

**Settings ▸ Macros** has:

- An **Add Macro** form for inline definition (name + expansion). The
  leading `\` is optional — if you omit it, MathNote prepends one.
- An **Import .tex Preamble File…** button that reads all
  `\newcommand` / `\def` / `\DeclareMathOperator` /
  `\DeclarePairedDelimiter` from a `.tex` file.
- A list of currently-defined macros with per-item delete buttons,
  plus a **Clear All Macros** button at the bottom.

Global macros apply to every document. File-local macros (defined in
the open document) override global ones with the same name.

---

## Insert Menu

All three Insert items operate on the focused document window only,
so multi-window sessions don't trigger panels in the wrong place.

- **Image…** (⇧⌘I) — pick an image. MathNote copies it into a
  `{docname}.assets/` sidecar folder next to the document and inserts
  the appropriate snippet at the caret:
  - `.md`: `<img src="{docname}.assets/foo.png" width="70%">`
  - `.tex`: `\includegraphics[width=0.7\textwidth]{{docname}.assets/foo.png}`
- **File Attachment…** (⇧⌘A) — any file type. Copied into the same
  `.assets/` folder, inserted as a clickable link:
  - `.md`: `[name](path)`
  - `.tex`: `\href{path}{name}`

  Clicking the link in the preview opens the file in its default
  application (PDF in Preview, spreadsheet in Numbers, …) via
  `NSWorkspace.open`.
- **Rotate Image Left** (⇧⌘L) / **Rotate Image Right** (⇧⌘R) —
  operates on the image referenced on the editor's current line
  (`\includegraphics{…}`, `<img src=…>`, or `![](…)`). Rewrites the
  file on disk so the rotation persists through PDF export and
  external LaTeX compilation. Bumps an internal cache-buster so the
  WebView re-fetches instead of serving the stale image.

All three require the document to have been saved at least once —
the sidecar folder sits next to the document on disk.

---

## External Editors

You can hand the current document off to an external editor of your
choice — TeXstudio, TeXShop, VS Code, BBEdit, Typora, Obsidian — for
any task MathNote doesn't cover (full LaTeX compile, advanced regex
search, vim keybindings, …).

Two ways:

- **Toolbar ↗ Open Externally**
- **File ▸ Open in External Editor (⌥⌘O)**

The first time you invoke this for a `.md` or `.tex` file, MathNote
prompts you to pick an application and remembers your choice. The
preferences are kept **per format**: `.md` and `.tex` have
independent slots so you can route each to its natural editor.

Hold ⌥ when invoking the toolbar button (or the menu item) to
re-prompt and switch the app for the current format. You can also
manage both slots from **Settings ▸ Editors** (clear, change, see the
chosen app's icon and full path).

---

## Scroll & Click Sync

MathNote keeps editor and preview aligned with three complementary
mechanisms.

### 1. Editor → preview scroll sync

When you scroll the editor, MathNote computes the **top visible line
number** and tells the preview to scroll to the element whose
`data-src-line` attribute matches (or the nearest one above). Because
it's line-based rather than pixel-fraction-based, the two panes stay
synchronized even when the rendered preview is a different height
than the source.

### 2. Double-click sync (preview → editor)

Double-click any block in the preview. MathNote reads its
`data-src-line`, scrolls the editor to that line, places the cursor
there, and flashes the line yellow. (Scrolling the preview alone
does not move the editor — preview-to-editor sync is double-click
only.)

### 3. ⌘-click jump-to-definition

⌘-click on `\ref{key}`, `\eqref{key}`, `\cref{key}`, `\autoref{key}`,
`\Cref{key}`, or `\label{key}` in the editor and MathNote jumps to
where that label was defined.

---

## Find & Replace

### In the editor

| Shortcut | Action |
|----------|--------|
| ⌘F | Find |
| ⌥⌘F | Find & Replace |
| ⌘G | Find Next |
| ⇧⌘G | Find Previous |
| ⌘E | Use Selection for Find |
| ⌘J | Jump to Selection |

These hit `NSTextView`'s native find bar. Regex, case-sensitivity,
and wrap-around are available from the find bar's menu.

### In the preview

| Shortcut | Action |
|----------|--------|
| ⇧⌘F | Open "Find in Preview…" |
| Return | Next match |
| ⇧Return | Previous match |
| ⎋ (Esc) | Close find bar |

Matches are highlighted yellow; the current match is highlighted
orange and scrolled into view. Find in preview searches the rendered
text (so it sees "Theorem" even if your source says
`\begin{theorem}`), but skips KaTeX-rendered math spans so your
query doesn't break rendered equations.

---

## Diagnostics Panel

Toggle with the ⚠ Diagnostics toolbar button.

The panel lists two kinds of diagnostics:

1. **Transformer diagnostics** — warnings from the LaTeX-to-markdown
   conversion step. Examples: arity mismatch in `\newcommand`,
   untranslated commands (info-level, shown as counts).
2. **KaTeX diagnostics** — every `katex-error` that the renderer
   produced. Click any item to jump to the source line.

The toolbar button shows a red badge with an error count when errors
are present, orange for warnings, invisible when clean.

---

## Theorem Sidebar

Toggle via the 1·2·3 (`list.number`) toolbar button. Shows every
theorem-like environment in the document (theorem, lemma,
proposition, corollary, definition, example, remark, proof,
conjecture, claim, observation, assumption, exercise, fact, note,
problem, question, solution). Each row shows the environment type,
optional `[title]`, `\label{}` if any, and the first line of the
body.

Click a row to jump the editor to that theorem.

---

## Diff Panel

Toggle via the ⇆ (`arrow.triangle.branch`) toolbar button or
**⇧⌘D**. Pins to the right edge of the window and shows a complete
line-by-line diff of the current buffer against the file as last
saved.

Each hunk is annotated with its starting line number — click *Line N*
to jump the editor caret there. The same change set drives the
in-editor diff gutter, so the panel and the gutter never disagree.

The diff updates on every preview refresh (⌘R or auto-refresh).
Saving the file empties the diff (everything is now "saved" so
nothing is changed).

---

## PDF Export

Press **⌥⌘E** (or **File ▸ Export as PDF…**) to export the current
preview to PDF. A save dialog picks the destination.

The output is **paginated to A4 with 1 inch (72 pt) margins on every
page**. Internally MathNote lays out the preview at content width,
captures it, then composites slices onto fresh A4 pages via
`CGPDFContext` — `WKPDFConfiguration`'s body padding only pads the
body, not page boundaries, so a manual composite step is needed to
get true per-page margins.

PDF export always renders in the light theme regardless of whether
blackboard mode is currently on; the chalkboard background, glow, and
chalk-dust grain are display-only.

---

## Settings

Open with **⌘,**. Five tabs.

### Macros
Add/remove/import user macros. See [User Macros](#user-macros).

### Snippets
Edit the trigger/expansion list. See [Snippets](#snippets).

### Colors
A `ColorPicker` per syntax category. Changes apply live to every open
document. Each row shows the hex code next to the picker for
copy/paste. **Restore Defaults** resets the palette to the shipping
one.

### Fonts
- **Editor Font** — 10–24 pt monospaced system font.
- **Preview Font** — 10–24 pt base size for the preview. KaTeX scales
  with this.
- **Editor ▸ Show Line Numbers** — toggles the left-hand line-number
  ruler.
- **Preview Font Family** — picker for the normal-mode preview body
  font. Presets: KaTeX Default (Computer Modern), Palatino, Times New
  Roman, Georgia, System Serif, System Sans-Serif, Charter. Math
  always renders in KaTeX's bundled fonts regardless of this choice.
- **Blackboard Font Family** — picker for the body font used when
  blackboard mode is on. See [Blackboard Mode](#blackboard-mode) for
  the full list.

### Editors
Per-format external editor preference. See
[External Editors](#external-editors).

---

## Keyboard Shortcuts

### Document
| Shortcut | Action |
|----------|--------|
| ⌘N | New document |
| ⌘O | Open |
| ⌘S | Save |
| ⇧⌘S | Save As |
| ⌘W | Close window |
| ⌘, | Settings |

### View
| Shortcut | Action |
|----------|--------|
| ⌘R | Refresh preview |
| ⇧⌘D | Toggle Diff panel |

### Editing
| Shortcut | Action |
|----------|--------|
| ⌘F | Find |
| ⌥⌘F | Find & Replace |
| ⌘G | Find Next |
| ⇧⌘G | Find Previous |
| ⌘E | Use Selection for Find |
| ⌘J | Jump to Selection |

### Preview
| Shortcut | Action |
|----------|--------|
| ⇧⌘F | Find in Preview |
| ⌥⌘E | Export Preview as PDF |

### Insert
| Shortcut | Action |
|----------|--------|
| ⇧⌘I | Insert Image… |
| ⇧⌘A | Insert File Attachment… |
| ⇧⌘L / ⇧⌘R | Rotate Image Left / Right |

### Other
| Shortcut | Action |
|----------|--------|
| ⌥⌘O | Open in External Editor |
| ⌘? | Quick Reference |

### Navigation
| Action | How |
|--------|-----|
| Jump to label definition | ⌘-click `\ref{…}` / `\label{…}` (and the cref / Cref / autoref / eqref family) |
| Jump editor to source of a preview block | Double-click the block in the preview |

---

## Tips for LaTeX Authors

### Keep math delimiters consistent
MathNote's extractor handles `$…$`, `$$…$$`, `\(…\)`, `\[…\]`, and
`\begin{equation}…\end{equation}` (plus `align`, `align*`, `gather`,
`gather*`, `multline`, `multline*`, `aligned`, `split`, `cases`, all
matrix environments, and `array`). If you mix styles they'll all
render — but sticking to one makes scroll sync cleaner.

### Label *inside* math environments
```latex
\begin{equation}\label{eq:main}
    E = mc^2
\end{equation}
```
is fine. MathNote strips `\label{…}` from the math block before
passing it to KaTeX (which doesn't know what to do with it), but
remembers the label for `\ref`/`\cref` resolution.

### `\newtheorem` declarations
The theorem-like environments listed in
[Theorem Sidebar](#theorem-sidebar) are recognized whether or not you
declared them with `\newtheorem`. The `[title]` optional argument is
honored.

### Paired delimiters with `*`
```latex
\DeclarePairedDelimiter{\norm}{\lVert}{\rVert}
```
In math both `\norm{x}` and `\norm*{x}` render correctly. The
starred form expands to `\left\lVert x \right\rVert`; the plain form
uses the fixed-height variant.

### Use `\cref` / `\Cref`
They render as `(label)` in the preview — human-readable even
without a bibliography.

### Hide drafts with `\begin{comment}…\end{comment}`
The whole block is stripped from the preview before any other
substitution runs, so you can keep work-in-progress notes inline
without them showing up in the rendered output.

### Keep bodies self-contained
The source-line sync works at block granularity (paragraphs,
headings, display math, theorem blocks). Putting each logical element
on its own blank-line-separated block gives the finest sync
resolution.

---

## Known Limitations

- **No full LaTeX compilation.** MathNote is for previewing while you
  write. Produce final PDFs with `pdflatex` / `xelatex` / `lualatex`
  via the **Open in External Editor** path or your usual toolchain.
- **TikZ is not rendered.** `\begin{tikzpicture}…\end{tikzpicture}`
  becomes a placeholder `[TikZ diagram — not rendered in preview]`.
  For TikZ previews, use your LaTeX toolchain.
- **Nested lists.** `itemize` / `enumerate` inside another list
  render each `\item` as literal text beyond the outer level. Flat
  lists work perfectly.
- **Unknown commands.** Any `\command` that's not a built-in, a
  registered macro, or in MathNote's known-commands list is passed
  through as text (and counted in the diagnostics panel). Define it
  in your preamble or as a global user macro.
- **`\input`/`\include`** — not followed. MathNote edits one file at
  a time.
- **Line-based sync** jumps to the *start* of the containing block.
  For finer granularity, split long paragraphs with blank lines.
