# CLAUDE.md — Dataflow Architectures for AI Course

> This file is read by Claude Code at the start of every session. It encodes project-specific context so we don't repeat ourselves.

## Project overview

A 6-week professional course on **AI accelerator architectures**, treating dataflow execution as an architectural class across GPU (NVIDIA), TPU (Google), Cerebras, Groq, and IBM Spyre. Primary author: Dr. Kaoutar El Maghraoui (IBM Research / Columbia). Target platforms: Cognitive Class (Q3 2026), Coursera.

## Repository structure

This course uses **two separate GitHub repositories** for access control:

```
dataflow-arch-for-ai/                          # PUBLIC — students
├── README.md, SYLLABUS.md, LICENSE.md, CLAUDE.md
├── docs/                                      # design-decisions, getting-started, vscode-workflow
├── lectures/week-NN-*/                        # slides.pdf, notes.md, readings.md
├── labs/week-NN-*/
│   ├── README.md
│   ├── starter/                               # student-facing notebooks
│   └── optional_spyre/                        # Spyre extension stubs (not solutions)
├── resources/                                 # landscape-map.md, reading-list.md, debugging-taxonomy.md
└── assessments/                               # public quiz prompts, final portfolio rubric

dataflow-arch-for-ai-solutions/                # PRIVATE — instructors/TAs only
├── README.md                                  # links back to public repo
├── tools/build_notebooks.py                   # SOURCE-OF-TRUTH generator: builds both starter + solution
├── labs/week-NN-*/
│   ├── solution/                              # full-worked notebooks
│   ├── rubric.md
│   └── common_mistakes.md
├── assessments/quiz-answers/
└── tools/sync-with-public.sh                  # rebuild starters and push to public repo
```

**Why two repos**: GitHub branch protection prevents writes, not reads. A protected `solutions` branch on a public repo is still publicly readable, and any solution ever committed to `main` (even if later removed) stays in the git history. Two separate access boundaries are the only way to actually hide solutions from students. See `docs/design-decisions.md` decision #4 for full rationale.

**Working-clone pattern**: clone both repos as siblings so VS Code sees both:

```
~/courses/dataflow/
├── public/        ← dataflow-arch-for-ai
└── solutions/     ← dataflow-arch-for-ai-solutions
```

Open VS Code on `~/courses/dataflow/` (the parent directory). Claude Code can @-mention files across both repos in a single conversation. Commit each side independently.

**Sync strategy**: solutions are the source of truth. `tools/build_notebooks.py` (in the solutions repo) generates both starter and solution notebooks from a single Python source. The generator writes starters to `../public/labs/week-NN-*/starter/`. After running it, commit each repo separately.

## Locked design decisions (do not re-litigate unless asked)

See `docs/design-decisions.md` for full rationale. Key decisions:

- **LLM used in labs**: `Qwen/Qwen2.5-0.5B-Instruct` — small enough for Colab free T4.
- **Grading model**: Hybrid — ~60% auto-graded checkpoints (`assert` cells), ~30% peer-reviewed written analysis, ~10% Landscape Map row.
- **Framing principle**: *Dataflow-first, vendor-second.* Every concept introduced as a general principle before any vendor is named. Spyre is a worked example, never the subject.
- **Comparative requirement**: Every lecture and lab references at least 3 of the 5 architectures on the Landscape Map.
- **Accessibility principle**: All core labs run on Google Colab free tier. Spyre content is always optional / Deep Dive sections.

## Style and voice

- **Technical level**: intermediate-to-advanced. Students have built models; this course is about the hardware beneath them.
- **Tone**: precise, not patronizing. Examples and measurements, not slogans.
- **Citations**: when citing a system's behavior, prefer primary sources — Jouppi TPU papers, Cerebras whitepapers, Groq MICRO/ISCA papers, NVIDIA architecture whitepapers, IBM Spyre announcements. Avoid vendor marketing pages.

## Lab notebook conventions

Every lab (starter and solution) follows this skeleton:

1. **Header cell** — course name, week, instructor, runtime requirement, expected time, learning objectives
2. **Environment check** — fail loudly if GPU runtime missing
3. **Numbered Parts** — each has an objective, scaffolded code with `TODO` markers, and a `Checkpoint` cell with `assert` statements that must pass for auto-grading
4. **Analysis section** — structured prompts for peer-reviewed written paragraphs
5. **Comparative callout** — explicit prompts tying back to the Landscape Map
6. **Landscape Map row update** — the row that this lab contributes
7. **Submission block** — writes `submission.json` for the portal upload
8. **Optional Spyre extension** — separate file in `labs/week-NN-*/optional_spyre/`

## Style tokens (Kaoutar's signature palette)

Used for matplotlib plots, notebook visualizations, and docx documents.

| Token | Hex | Usage |
|---|---|---|
| Terracotta (primary) | `#B85C2C` | Accents, compute-bound nodes, H2 borders |
| Cream (callouts) | `#FAF6F2` | Callout backgrounds, figure backgrounds |
| Dark text | `#1A1A1A` | Primary text, H3 |
| Secondary | `#5C5C5C` | Secondary text, memory-bound nodes, edges |
| Borders | `#D4C4B8` | Dividers, table borders |

**Font (for docx)**: Arial. See `docs/design-decisions.md` for full docx style spec.

## Code conventions

- **Python style**: Follow PEP 8. Black-formatted. Max line length 100.
- **Notebooks**: Every code cell should run top-to-bottom without hidden state. No bare `!pip install` in the middle — consolidate into the first setup cell.
- **Assertions in Checkpoint cells**: use wide ranges (accept 2–3× variation) because Colab hardware varies (T4 vs occasionally K80/A100).
- **Imports**: one import per line for `torch`/`torch.nn`-level packages; grouped imports for `from transformers import ...` is fine.
- **No proprietary dependencies in the starter path.** Every package must be on PyPI and installable on Colab free tier.

## Build and test

- **Build**: notebook generation scripts in `tools/` (in the **solutions repo**) use raw JSON (not `nbformat`) so they work without extra dependencies. From a working clone with both repos as siblings, run `python tools/build_notebooks.py` from the solutions repo root — it regenerates both starter (writes to `../public/labs/...`) and solution (writes to `./labs/...`) notebooks from a single source.
- **Smoke test**: `.github/workflows/smoke-test-notebooks.yml` (in the public repo) runs each starter notebook top-to-bottom nightly on a GPU runner. If a notebook breaks due to a PyTorch or Colab environment change, this catches it before students report it.
- **Validate a single notebook**: `jupyter nbconvert --to notebook --execute <path>` inside a fresh Colab-equivalent environment.

## Working patterns

- **Plan mode first** for any task that touches more than one file. Review the plan before accepting.
- **Start each new week's lab** by reading the existing Week 1 and Week 2 labs for template consistency — the Parts structure, Checkpoint style, Analysis rubric pattern should stay uniform across all 6 weeks.
- **For notebook edits**: prefer to regenerate from the builder script when doing large changes; use direct notebook edits only for small text fixes. This keeps the source-of-truth in one place (`tools/build_notebooks.py`).
- **When drafting new content**: write the Landscape Map row *first*, then work backwards into the lab tasks. The row is the learning goal; the lab is how students earn it.

## Prohibited patterns

- **No default Word template.** Docx outputs must use the terracotta/cream palette specified above, not blue corporate defaults.
- **No vendor marketing copy** in lecture notes. If a claim needs support, cite the primary paper.
- **No `nn.MultiheadAttention`** in `torch.fx.symbolic_trace` examples — it has internal control flow that breaks fx. Use the explicit Q/K/V projections pattern from `TinyTransformerBlock` in Lab 2.
- **No unicode bullets in docx output** — use `LevelFormat.BULLET` via docx-js. See `docs/design-decisions.md`.
- **Never commit `submission.json`** — it's in `.gitignore`. Students produce it; we never ship an example.

## Important references inside the repo

- `docs/design-decisions.md` — full record of locked decisions and their rationale
- `resources/landscape-map.md` — the running reference table that grows each week
- `labs/week-01-profiling/README.md` — example of the lab README format all weeks should follow
- `labs/week-02-fx-graph/solution/lab2_fx_graph_solution.ipynb` (in the **solutions repo**) — the canonical lab-solution structure including TA notes section

## External references (when needed, fetch via web)

- PyTorch torch.fx docs: https://pytorch.org/docs/stable/fx.html
- PyTorch Profiler tutorial: https://pytorch.org/tutorials/recipes/recipes/profiler_recipe.html
- Jouppi et al., "In-Datacenter Performance Analysis of a TPU" (ISCA 2017) — foundational TPU reference
- Williams, Waterman, Patterson, "Roofline" (CACM 2009) — the canonical Roofline paper
- DeepMind/Google JAX Scaling Book: https://jax-ml.github.io/scaling-book/ — current reference on scaling on TPUs

---

*Keep this file under 200 lines for reliable adherence (Claude attends to early instructions more than late ones).*
