Produce a three-section to-do list for the attached note. The note is a snapshot of in-progress research work; expect open threads and unfinished thoughts as the rule rather than the exception.

Read the source AND any attached photos AND any reference PDFs deeply enough to understand the substance — what the author is trying to prove, what techniques they're using, what's stuck. If a User Background page is present, read it too; the user's research area, papers in their field, and current courses inform what kinds of follow-ups will actually be useful.

Section 1 — Open threads (extracted from the note).
Identify every open thread: unfinished computations, "check this" margin notes, unresolved questions, half-derivations the author trailed off on, and any step the author flagged for verification (with `[?]`, `% CHECK`, "is this right?", "verify", etc.). Include threads from handwritten margin notes in photos.

Output as a flat bulleted list. For each item, give a short label naming what the thread is, then a one-or-two-sentence explanation of the motivation — what's at stake, why this thread matters to the larger argument, what would be unlocked by closing it. The substance, not the location (you may mention location parenthetically).

Order by the author's apparent priority — items explicitly flagged first, then inferred questions, then trailed-off derivations.

Extract only what is in the note — do not invent threads.

Section 2 — Generalizations (concrete next-step directions for the user).
Suggest a broad set of generalization directions for the most concrete result(s) in the note. If the note has a clear main result, generalize that; if the note is exploratory without one, pick the most fully-stated lemma/proposition.

If a User Background page is present, prefer directions that connect to the user's research area, recent papers, or current teaching — these are the generalizations they're most likely to actually pursue. Generalizations may extend into adjacent fields the user actively works in (mathematical physics, mathematical finance, mathematical biology, theoretical CS, etc.); stay concrete and grounded in their work.

Cross-field speculative analogies — "this loosely echoes the structure of X in field Y" — belong in New Perspective and should not appear here.

Pick axes appropriate to the note's subject area. Common axes include: weakening hypotheses; strengthening conclusions; extending to a wider class of objects; relaxing regularity; allowing dependence on additional variables; identifying which hypotheses are essential versus removable; replacing finite / discrete / commutative / abelian structure with its more general counterpart (or vice versa for the analytic direction); changing the ambient category, ground field, or base topology. Skip axes that don't apply.

Aim for breadth over depth: each direction is one or two sentences so the user can scan the menu and decide which to pursue in a follow-up. If you cite a specific counterexample or known result to justify a direction, mark unverifiable assertions with [! ...] rather than stating them as fact.

If the note's content does not admit useful generalization (e.g., it's a pure computation, a worked example, or notes on a specific concrete object), say so plainly in one sentence rather than producing thin filler.

Section 3 — References (related work the user might benefit from reading).
For the threads in Section 1 and the directions in Section 2, list 3–7 of the most relevant references you can verify. Useful categories: research papers, canonical textbooks, survey articles, well-cited arXiv preprints. Do not bias toward recent work — in mathematics the seminal reference is often decades or a century old, and rediscovery of historical results is common; an Euler / Cauchy / Riemann / Banach paper can be exactly what the user needs to read. Prefer references close to the user's research area (per User Background) — both because they're more useful to the user and because you're more likely to be sure of them.

If you have web search available, use it to verify titles, authors, years, and venues. If you don't, rely on training data but flag uncertainty more aggressively.

Format each entry as a flat bulleted list:
- Author(s) (year), "Title" — Venue or arXiv ID. One-line note on what the reference contains and why it matters here. → relates to: Section 1 item N / Section 2 direction M.

Hard rule against hallucination: do not invent a citation. If you cannot verify a paper / book / result exactly as you'd cite it, either omit the entry or flag uncertainty with [! ...] inline (e.g., "[! cannot verify exact citation, but I recall a result by Smith et al. on this from ~2018]"). The user inspects citations carefully; one fabricated entry erodes trust in the whole list.

If you cannot find any verifiable references, output the heading "## References" followed by a single sentence: "No verifiable references identified." Do not pad the section.
