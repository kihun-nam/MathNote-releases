Clean up and organize the attached note. Treat math from the source as read-only content; treat structure and prose as freely editable.

Before rewriting, read the source AND any attached photos AND any reference PDFs deeply enough to understand the substance — what the author is proving, what techniques they're using. If a User Background page is present, read it too; the user's research area, papers in their field, and current courses inform what terminology, section organization, and notation conventions will match their style.

Section 1 — Reorder for logical flow.
In a math note that means: notation declared before first use; definitions before the lemmas / theorems that use them; hypotheses listed adjacent to the statements they apply to; lemmas before the theorems they support. If the note uses non-trivial notation and has no notation block at the top, add one collected from notation already present in the source — do not invent symbols.

Section 2 — Group, deduplicate, segment.
- Merge fragments that re-state the same idea in different words back into the same place.
- For scratch attempts and abandoned threads: where they merge naturally into the relevant section, do so. Where they don't, leave them in place untouched — this app is for scratch work; do not relocate, summarize, or drop.
- Split aligned derivations longer than ~5 lines into either numbered steps ("Step 1: ..., Step 2: ...,") or independently-meaningful named lemmas. Pick one convention and use it consistently in your output.

Section 3 — What stays untouched.
Mathematical content from the source — equations, theorem statements, definitions, derivations — is preserved verbatim. Do not simplify, re-derive, or silently correct. If a step looks wrong, flag it with [! ...] inline AND state in 1–2 sentences what looks suspect — e.g., "[! sign on line 3: should this be $-\nabla f$ rather than $+\nabla f$? earlier convention has the gradient flowing inward]" — so the author can locate and adjudicate quickly.

Section 4 — Photos, macros, reference PDFs.
- Attached photos: follow SOURCE PRIORITY — interpret in source notation and integrate (with [+ ...] / <!-- INS START --> ... <!-- INS END -->) whatever the source doesn't already cover. Do not transcribe a photo whose content is already in the source.
- Reference PDFs are context for you, not material to merge into the note.
- If the bundle contains a user-macros page, prefer those macros in the output: write `\R` if the author has `\newcommand{\R}{\mathbb{R}}`. Do not expand macros inline, and do not redefine them in the body.

Section 5 — Tags.
Add a single line of 3–5 hashtag tags directly under the document's H1 title (or at the very top if there is no title). Each tag should name a topic, technique, named theorem, or named object that is genuinely characteristic of the note — useful as a search keyword. Format: lowercase, hyphen-separated, space-separated on one line. Example: `#heat-equation #fourier-transform #heat-kernel #schwartz-class`. Prefer well-known mathematical names (techniques, equations, theorems, function spaces, named inequalities) over generic labels.

If a User Background page is present, prefer terminology and named objects from the user's research area when both are valid (e.g., `#fokker-planck` if they work in stochastic PDE; `#rough-paths` if they work in pathwise stochastic analysis), since those tags will be more useful as search keywords for them specifically.

Example.

<input>
# Heat equation initial value problem

We want $u$ solving
$$
u_t - \tfrac{1}{2} u_{xx} = 0 \quad \text{on } \mathbb{R} \times (0, T], \qquad u(x, 0) = f(x).
$$

Try Fourier: $\hat u(\xi, t) = \int e^{-i \xi x} u(x,t)\, dx$. Then $\hat u_t = -\tfrac{1}{2} \xi^2 \hat u$, so $\hat u(\xi, t) = e^{-\xi^2 t / 2} \hat f(\xi)$. Inverting gives $u(x,t) = (G_t * f)(x)$ with $G_t(x) = \frac{1}{\sqrt{2 \pi t}} e^{-x^2 / 2t}$.

[? does this work for non-smooth $f$? check]

Definition. The Schwartz space $\mathcal{S}(\mathbb{R})$ is the set of $f \in C^\infty$ s.t. $\sup_x |x|^\alpha |\partial^\beta f(x)| < \infty$ for all $\alpha, \beta$.

Lemma. If $f \in \mathcal{S}$ then $G_t * f \to f$ uniformly as $t \to 0^+$.

For non-smooth $f$ I tried Lebesgue dominated convergence but $G_t$ has no uniform $L^1$ bound in $t$, so it fails. Maybe Plancherel?

Notation: $\langle\cdot,\cdot\rangle$ is $L^2$ inner product; $\|\cdot\|$ is $L^2$ norm.

Proof of lemma. Let $f \in \mathcal{S}$. Then
\begin{align*}
(G_t * f)(x) - f(x) &= \int G_t(y) [f(x-y) - f(x)]\, dy \\
&= \int_{|y| < \delta} G_t(y) [f(x-y) - f(x)]\, dy \\
&\quad + \int_{|y| \geq \delta} G_t(y) [f(x-y) - f(x)]\, dy.
\end{align*}
The first is $\leq \|f'\|_\infty \delta$. The second is $\leq 2\|f\|_\infty \cdot 2(1 - \Phi(\delta/\sqrt{t})) \to 0$ as $t \to 0^+$.
</input>

<output>
# Heat equation initial value problem

#heat-equation #fourier-transform #heat-kernel #schwartz-class #mollification

## Notation
$\langle \cdot, \cdot \rangle$ — $L^2$ inner product. $\|\cdot\|$ — $L^2$ norm.

## Setup

**Definition.** The Schwartz space $\mathcal{S}(\mathbb{R})$ is the set of $f \in C^\infty$ s.t. $\sup_x |x|^\alpha |\partial^\beta f(x)| < \infty$ for all $\alpha, \beta$.

We seek $u$ satisfying
$$
u_t - \tfrac{1}{2} u_{xx} = 0 \quad \text{on } \mathbb{R} \times (0, T], \qquad u(x, 0) = f(x).
$$

## Solution via Fourier transform

Take $\hat u(\xi, t) = \int e^{-i \xi x} u(x,t)\, dx$. Then $\hat u_t = -\tfrac{1}{2} \xi^2 \hat u$, so $\hat u(\xi, t) = e^{-\xi^2 t / 2} \hat f(\xi)$. Inverting,
$$
u(x,t) = (G_t * f)(x), \qquad G_t(x) = \frac{1}{\sqrt{2 \pi t}} e^{-x^2 / 2t}.
$$

[! does this work for non-smooth $f$?]

For non-smooth $f$: Lebesgue dominated convergence fails because $G_t$ has no uniform $L^1$ bound in $t$. Plancherel may be the right tool — to revisit.

## Convergence to initial data

**Lemma.** If $f \in \mathcal{S}$ then $G_t * f \to f$ uniformly as $t \to 0^+$.

*Proof.* Let $f \in \mathcal{S}$.

**Step 1.** Decompose
$$
(G_t * f)(x) - f(x) = \int G_t(y)\bigl[f(x-y) - f(x)\bigr]\, dy.
$$

**Step 2.** Fix $\delta > 0$ and split the integration domain:
$$
\int G_t(y) \bigl[f(x-y) - f(x)\bigr]\, dy = \int_{|y| < \delta} + \int_{|y| \geq \delta}.
$$

**Step 3.** Bound the inner region: $\bigl|\int_{|y|<\delta}\bigr| \leq \|f'\|_\infty \, \delta$.

**Step 4.** Bound the outer region: $\bigl|\int_{|y|\geq\delta}\bigr| \leq 2\|f\|_\infty \cdot 2\bigl(1 - \Phi(\delta/\sqrt{t})\bigr) \to 0 \text{ as } t \to 0^+. \qquad \square$
</output>
