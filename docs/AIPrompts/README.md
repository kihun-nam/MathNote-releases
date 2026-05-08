# Using the AI Prompts Manually (macOS)

MathNote's **Ask AI** feature (available on iOS) bundles your note,
photos, and reference PDFs into a single request and sends it to
Claude with one of the prompts below. On macOS the feature is not
yet built in, but you can get the same results by copying a prompt
into any Claude interface yourself.

## Quick start

1. **Open your note** in MathNote and copy the LaTeX/Markdown source
   (⌘A, ⌘C).
2. **Open Claude** — [claude.ai](https://claude.ai), the Claude
   desktop app, or the Claude API.
3. **Paste one of the prompts below** into the message box.
4. **Paste your note** after the prompt (or attach the `.tex` / `.md`
   file directly).
5. If your note references photos or PDFs, **attach those files** to
   the same message — the prompts are written to read them.
6. **Send** and review the response.

## The prompts

Each prompt is a standalone instruction file. Open it, copy the full
text, and paste it at the start of your message to Claude.

| Prompt | What it does |
|--------|-------------|
| [verify.md](verify.md) | Line-by-line proof verification — flags every step it cannot confirm, erring on the side of false positives. |
| [fillTheGap.md](fillTheGap.md) | Fills in skipped proof steps and answers margin questions (`TODO`, `WHY?`, `% CHECK`). |
| [reformat.md](reformat.md) | Restructures and cleans up prose and organization while leaving math untouched. |
| [todo.md](todo.md) | Produces a three-section to-do list: open threads, potential improvements, and next steps. |
| [examplesAndCounterexamples.md](examplesAndCounterexamples.md) | Generates worked examples illustrating the main result and searches for counterexamples to test its hypotheses. |
| [newPerspective.md](newPerspective.md) | Three short, speculative reflections — novelty check, cross-field analogies, and alternative proof strategies. |
| [source-priority.md](source-priority.md) | Bundling instructions that tell the AI how to prioritize your source vs. reference PDFs vs. photos. Paste this before any other prompt when you attach multiple files. |
| [user-background-guidance.md](user-background-guidance.md) | Optional author profile — research area, preferred notation, papers, courses. Paste this so the AI calibrates explanations to your level. |

## Combining prompts

The prompts are composable. A typical session looks like:

```
[paste source-priority.md]          ← tells the AI how to read your bundle
[paste user-background-guidance.md] ← your research profile (optional)
[paste verify.md]                   ← the task you want
[paste or attach your .tex note]
[attach photos / reference PDFs]
```

`source-priority.md` and `user-background-guidance.md` are
context-setters — they go first, before the task prompt.

## Tips

- **One task per message** works best. Run `verify`, read the
  response, then start a new message with `fillTheGap` rather than
  asking for both at once.
- **Attach, don't paste, PDFs.** Claude can read PDF attachments
  directly; pasting their text loses formatting and figures.
- **Photos matter.** If you have handwritten margin notes, board
  photos, or annotated printouts, attach them. The prompts are
  designed to read visual content alongside the source.
- **Use the latest Claude model** (Opus or Sonnet) for best results
  with mathematical reasoning.
