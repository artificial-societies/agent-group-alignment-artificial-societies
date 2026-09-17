# Agent Group Alignment

Research sources and reproduction materials for two Artificial Societies papers and a
blog post on **AI agent group alignment** — the problem of keeping a population of agents
aligned with human values, as opposed to aligning any single agent.

Each paper is accompanied by the Jupyter notebook that produces its figures, so every
numerical claim in the PDFs can be re-derived from this repository. The blog post
synthesises both papers; its simulation figures are copies of those notebook outputs.

> Blog posts: https://societies.ai/group-alignment

## Contents

```
agent-based-modelling/
    paper.tex / paper.pdf                     Agent Based Modeling for Group Alignment
    references.bib                            15 references
    hypotheses_numerical_simulation.ipynb     all four figures
    figures/                                  hypothesis_1...3, hypothesis_3_solution

principal-agent-problem-framework/
    paper.tex / paper.pdf                     Group Alignment through the Principal-Agent Lens
    group-alignment-numerical-simulation.ipynb   all three figures
    figures/                                  fig1..fig3

AI_Researchers_Should_Use_Group_Alignment_to_Reduce_P_doom/
    main.tex / main.pdf                       blog post synthesising both papers
    blog_style.sty                            layout (NeurIPS-derived)
    references.bib                            biblatex + biber
    sections/                                 key-takeaways, introduction,
                                              individual-and-group-alignment,
                                              experiments, conclusion
    images/                                   six notebook figures + TikZ diagrams

requirements.txt                              pinned Python dependencies
```

Bibliography handling differs across the three: the ABM paper uses BibTeX
(`references.bib`), the principal-agent paper carries an inline `thebibliography`,
and the blog post uses `biblatex` with a Biber backend (`references.bib`).

---

## Paper 1 — Agent Based Modeling for Group Alignment

A deliberately minimal stochastic model of a **mixed-speed population**. Fast agents (AI) and
slow agents (humans) are placed at random on an *n × n* grid with a Moore neighbourhood.
Every agent carries one bit — the *origin* of the information it holds — and exchanges it
through a push–pull cycle governed by four cross-type acceptance probabilities and a speed
ratio ρ. No agent has any preference for its own type; fast agents simply take more turns.

The outcome measure is the **C-index**: the ratio of slow-origin to fast-origin information at
equilibrium, divided by the ratio of slow to fast agents. C = 1 means human-origin information
is represented in proportion to the number of humans.

| Hypothesis | Closed-form prediction | Result |
|---|---|---|
| 1. Fast agents mainly hear from other fast agents | ρ/(ρ+1) — 0.5 at ρ=1, 0.99 at ρ=100 | supported |
| 2. Fast-origin information crowds out slow-origin | C falls as 1/ρ, even at a 50/50 split | supported |
| 3. Raising P_sf restores balance | C = 1 at P_sf\* = ρ·P_fs | supported, but only while P_sf\* ≤ 1 |

The third result is the practically interesting one. A single consultation lever fails once
ρ·P_fs > 1, because the required P_sf\* would exceed one and P is a probability. Past that
point the slow population must *also* become less receptive to fast-origin information, which
is what the fourth figure sweeps.

The headline reading: the imbalance is **structural, not intentional** — it follows from a
difference in speed alone, and it is corrected by adjusting how agents weigh one another
rather than by retraining any individual agent.

## Paper 2 — Group Alignment through the Principal-Agent Lens

Delegation from a human principal to AI agents reproduces the classical principal-agent
problem, with misalignment driven by **information asymmetry**: finite context windows,
unobservable black-box policies, selectively revealed reasoning traces, and humans with
limited time to check everything. Observation cost exceeds execution cost, and the
interaction is one-to-many, so full oversight does not scale.

Each agent holds a **hidden latent norm** θ and a **visible output norm** y; the principal
observes only y. *Agency loss* — the gap between intended outcome and system behaviour — is
the loss function to minimise. The proposal is that instilling **social norms** between agents
is a cheaper alignment lever than direct observation of each agent.

| Hypothesis | Result |
|---|---|
| 1. Oversight and conformity together achieve stable alignment | Conformity alone is mean-preserving; alignment improves only when human norms are injected (β > 0) |
| 2. Strong inter-agent norms let principals observe fewer agents | Small increases in norm strength α sharply cut observation burden while keeping agency loss under tolerance |
| 3. Without internalisation, alignment decays when oversight stops | The internalisation index η is decisive — internalising agents retain human-norm influence after withdrawal |

## Blog post — AI Researchers Should Use Group Alignment to Reduce P(doom)

LaTeX source: [`AI_Researchers_Should_Use_Group_Alignment_to_Reduce_P_doom/`](AI_Researchers_Should_Use_Group_Alignment_to_Reduce_P_doom/). Entry point is `main.tex`.

A synthesis of the two papers, written as an Artificial Societies blog post. The
argument is that **individual alignment does not compose**: techniques that make a
single agent safe (SFT, RLHF, chain-of-thought monitoring) do not guarantee that a
collective of such agents will remain safe. Group alignment is defined as an
*interaction structure*, including the shared conventions that emerge between agents,
that reliably produces joint behaviour respecting human values.

The proposed training environment is a **mixed swarm**: frontier agents interacting
with accurate agentic models of human behaviour. Papers 1 and 2 are the proof of
concept — a speed-asymmetric population on a grid, then a principal-agent swarm with
peer influence, sparse oversight, and internalisation.

Three conditions in the definition: *composition* (joint behaviour can fail even when
every agent meets its own specification), *durability* (conventions hold under pressure
to abandon them), and *incentive compatibility* (the interaction structure must not
make violating human values the preferred option).

The experiments section reuses the notebook figures under different filenames; two
TikZ diagrams (`moore-neighbourhood.tex`, `fast-slow-communication.tex`) are drawn in
LaTeX rather than exported from a notebook. `fast-slow-communication.tex` is present
but not `\input` from `main.tex`.

---

## Reproducing the figures

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
jupyter notebook
```

Every simulation uses a seeded NumPy generator, so figures reproduce exactly. Outputs are
committed, so the results can be read without running anything.

Which notebook section produces which figure:

| Figure | Notebook | Section |
|---|---|---|
| `hypothesis_1.png` | `hypotheses_numerical_simulation.ipynb` | §5 — sweep the speed ratio ρ ∈ {1, 3, 10, 100} |
| `hypothesis_2.png` | same | §6 — fast-origin information drowns out slow-origin |
| `hypothesis_3.png` | same | §10 — Experiment A, sweep the consultation lever P_sf |
| `hypothesis_3_solution.png` | same | §11 — Experiment B, both levers at ρ = 10 |
| `fig1.png` | `group-alignment-numerical-simulation.ipynb` | §1 — does the swarm converge to the human norm? |
| `fig2.png` | same | §2 — how many agents must be observed? |
| `fig3.png` | same | §3 — what happens when oversight is turned off? |

How those outputs appear in the blog post (`AI_Researchers_Should_Use_Group_Alignment_to_Reduce_P_doom/images/`):

| Blog figure | Notebook source |
|---|---|
| `slow-origin-information.png` | `hypothesis_2.png` |
| `consultation-balance.png` | `hypothesis_3.png` |
| `joint-probability-balance.png` | `hypothesis_3_solution.png` |
| `social-conformity-convergence.png` | `fig1.png` |
| `oversight-requirement.png` | `fig2.png` |
| `internalisation.png` | `fig3.png` |

Figures are exported by hand from the notebooks rather than written by `savefig`.

**Runtime.** The principal-agent notebook runs in seconds. The ABM notebook is the slow one:
Experiment B runs a few hundred seeds per parameter point in parallel through `joblib` and
takes a while.

## Building the papers

```bash
cd "agent-based-modelling"
pdflatex paper.tex && bibtex paper && pdflatex paper.tex && pdflatex paper.tex

cd "../principal-agent-problem-framework"
pdflatex paper.tex && pdflatex paper.tex

cd "../AI_Researchers_Should_Use_Group_Alignment_to_Reduce_P_doom"
pdflatex main.tex && biber main && pdflatex main.tex && pdflatex main.tex
```

The ABM paper needs the BibTeX pass plus two further `pdflatex` runs to settle citations; the
principal-agent paper needs `pdflatex` twice for cross-references; the blog post needs
**Biber** (not BibTeX) plus two further `pdflatex` runs. The papers read figures from
their own `figures/` directory; the blog post reads from `images/`.

## Citation

```bibtex
@misc{chen2026abm,
  author = {Chen, Yitian},
  title  = {Agent Based Modeling for Group Alignment},
  year   = {2026},
  note   = {Artificial Societies, University of Cambridge}
}

@misc{chen2026pa,
  author = {Chen, Yitian},
  title  = {AI Agent Group Alignment through the Lens of
            principal-agent-problem-framework},
  year   = {2026},
  note   = {Artificial Societies, University of Cambridge}
}

@misc{chen2026pdoom,
  author = {Chen, Yitian},
  title  = {AI Researchers Should Use Group Alignment to Reduce P(doom)},
  year   = {2026},
  note   = {Artificial Societies blog post}
}
```

## License

MIT — see `LICENSE.txt`.
