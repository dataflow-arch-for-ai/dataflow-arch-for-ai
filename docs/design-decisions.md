# Design Decisions

> A record of decisions made during course development, with rationale. New sessions of Claude Code (or new collaborators) should read this before proposing changes. If you want to revisit one of these, explicitly flag it as a *revisit* — don't silently propose alternatives.

---

## 1. Course positioning — *Dataflow-first, vendor-second*

**Decision**: treat dataflow execution as an architectural class. Introduce every concept as a general principle before naming any vendor. Use Spyre as a worked example deeply but never as the subject.

**Rationale**: the course's unique position in the graduate-ML-systems landscape is this comparative framing. Stanford CS 217 is hardware-designer focused; MIT 6.5940 is efficiency-focused; UW CSE 599W is breadth-across-the-stack. No existing course owns the comparative dataflow slice. Sliding into Spyre-centric content would collapse into vendor marketing and lose the differentiation.

**Consequences**: every lecture must reference ≥ 3 of the 5 architectures (GPU, TPU, Cerebras, Groq, Spyre). Every lab's "Analysis" section includes at least one comparative prediction prompt.

---

## 2. Model for labs — Qwen 2.5 0.5B Instruct

**Decision**: standardize every core lab on `Qwen/Qwen2.5-0.5B-Instruct`.

**Alternatives considered**:
- GPT-2 small (124M) — too small and architecturally dated; GQA/RoPE/SwiGLU absent
- Llama 3.2 1B — gated on HuggingFace acceptance; blocks free access
- TinyLlama 1.1B — widely used in teaching but older architecture
- Qwen 2.5 0.5B — **chosen**: modern (GQA, RoPE, SwiGLU), fits Colab free T4, no gating

**Consequences**: lab measurements assume this model; if Qwen releases 3.x as the new canonical small model, the labs should migrate (single-line change in setup cell).

---

## 3. Grading model — hybrid auto + peer review

**Decision**: ~60% auto-graded checkpoints (`assert` cells), ~30% peer-reviewed written analysis, ~10% Landscape Map row completion. Plus weekly 5–8 question MCQ quizzes (~15% of total course grade, outside the per-lab split).

**Rationale**: 
- Pure auto-grading can't assess the comparative *reasoning* that's the course's highest-value output.
- Pure peer review scales badly and has known variance problems (25% of Coursera courses still use peer review, down from 39% in 2023 — the trend is away from it).
- Hybrid preserves the high-value analysis assessment while making most of the grade fast and deterministic.

**Consequences**: every lab has TODO-scaffolded code cells with Checkpoint assertions, plus explicit written-analysis prompts with a 2-axis rubric (specificity × correctness, 0–10 each).

---

## 4. Repository structure — two separate repos

**Decision (revised April 2026)**: two separate GitHub repositories. `dataflow-arch-for-ai` (public) for students; `dataflow-arch-for-ai-solutions` (private) for instructor content (solutions, rubrics, quiz answers, common-mistakes guides, the notebook generator).

**Alternatives considered (and rejected after revision)**:
- *Single repo with `solutions` branch + branch protection*. Originally chosen, then revised. Rejected because branch protection prevents writes but not reads — a "protected" branch on a public repo is still publicly readable. Worse, any solution ever committed to `main` (even if later removed via `git rm`) remains discoverable in git history. Squashing history breaks blame and is fragile. Public repos cannot meaningfully hide a branch on standard GitHub plans.
- *Solutions in `solutions/` folder with `.gitignore`*. Rejected — `.gitignore` only prevents tracking new files. Anything ever committed remains in history.
- *Single private repo with public mirror that strips solutions on build*. Rejected — adds a CI dependency that becomes a single point of failure and obscures the source of truth.

**Consequences**:
- Two access boundaries (one per repo), enforced by GitHub repo permissions, which are the standard and well-tested model.
- Instructors clone both as siblings and open VS Code on the parent directory — Claude Code can @-mention files across both repos in one session.
- Solutions repo is the source of truth. `tools/build_notebooks.py` lives in solutions and writes starters to `../public/labs/...` on each rebuild. Each repo committed and pushed separately.
- The public repo's `.gitignore` no longer needs the `labs/**/solution/` exclusion — solutions are never present in the public repo at all.
- Solution notebooks for Weeks 1 and 2 (currently shipped in the v1.1 tarball under `labs/.../solution/`) must be moved into the new solutions repo on initial setup; they should not be committed to the public repo.

---

## 4a. Sync workflow between the two repos

**Working-clone pattern**:

```
~/courses/dataflow/
├── public/        ← dataflow-arch-for-ai
└── solutions/     ← dataflow-arch-for-ai-solutions
```

Open VS Code on `~/courses/dataflow/`. Both repos visible in the explorer. Edit either side, commit each independently.

**Generator-driven sync**: notebooks are generated from a Python source in `solutions/tools/build_notebooks.py`. Running it produces both starter (writes to `../public/labs/...`) and solution (writes to `./labs/...`) variants. This guarantees they stay structurally aligned.

**Manual sync for non-notebook content**: lecture notes, READMEs, the Landscape Map, and the syllabus live only in the public repo. The solutions repo references these via the working-clone parent directory rather than duplicating.

---

## 5. Lab template — uniform 8-section structure

**Decision**: every lab starter + solution follows:
1. Header (course, week, objectives, time, runtime)
2. Environment check cell
3. Numbered Parts with TODO + Checkpoint
4. Analysis section (peer-reviewed prompts)
5. Comparative callout
6. Landscape Map row update
7. Submission block (`submission.json`)
8. Optional Spyre Deep Dive link

**Rationale**: consistency reduces cognitive load for students (once they've done Lab 1 they know the shape of Lab 2); easier for TAs to calibrate grading; simpler CI smoke-testing.

**Consequences**: when drafting new weeks, *start from the template skeleton*, don't reinvent.

---

## 6. Style palette — Kaoutar's terracotta signature

**Decision**: use the following palette consistently for plots, visualizations, and docx documents.

| Token | Hex | Usage |
|---|---|---|
| Terracotta | `#B85C2C` | Accents, compute-bound nodes, H2 borders |
| Cream | `#FAF6F2` | Callout backgrounds, figure backgrounds |
| Dark text | `#1A1A1A` | Primary text, H3 |
| Secondary | `#5C5C5C` | Secondary text, memory-bound nodes |
| Border | `#D4C4B8` | Dividers, table borders |

**Rationale**: consistent visual identity across materials; avoids the generic "corporate blue" default that makes MOOCs look interchangeable; readable in both light and dark themes.

**Consequences**: in notebooks, declare these as constants at the top of any cell that renders plots. In docx: see the full docx style spec (kaoutar-docx-style skill).

---

## 7. FX tracing approach — TinyTransformerBlock, not full LLM

**Decision**: for Lab 2 (FX graph visualization), use a custom `TinyTransformerBlock` that mirrors Qwen's structure (pre-norm, Q/K/V projections, SwiGLU-adjacent FFN) but is fully `torch.fx.symbolic_trace`-compatible.

**Rationale**: modern LLMs (including Qwen) are not cleanly fx-traceable because of KV cache handling, position embedding math, and generation loops. Pushing students through `torch.export` or Dynamo in Week 2 would front-load compiler internals before the "read the graph" intuition is in place. Week 3 covers `torch.export` properly.

**Consequences**: the block is artificial but faithful. Checkpoint ranges are calibrated against it. Students who object that "the real Qwen doesn't trace" get the week-3 preview answer.

---

## 8. Grading thresholds — deliberately wide

**Decision**: Checkpoint assertions use wide ranges (2–3× variation) rather than tight numerical matches.

**Rationale**: Colab hardware varies. A student assigned a T4 will get different numbers than one on an A100 or (rarely) a K80. Tight thresholds produce false failures.

**Example**: Lab 1 Checkpoint 2 asserts `speedup > 2x` (actual typical range: 20–50× on T4). Lab 2 Checkpoint 3 asserts `5e8 < total_flops < 5e9` (actual value at default params: ~2.2e9).

**Consequences**: when debugging student issues, first check if their runtime type changed; don't assume a checkpoint failure means wrong code.

---

## 9. Scope exclusions (for now)

Intentionally out-of-scope to avoid bloat:

- **Training workloads**: course is inference-focused. Distributed training, FSDP, ZeRO are explicitly excluded. (Consider a sibling course.)
- **Multi-device / scale-out interconnects**: single-accelerator focus. TPU pods, Cerebras wafer-scale routing, NVLink/NVSwitch topologies mentioned briefly in Week 6 but not dissected. (Likely a future Week 7.)
- **MLIR internals**: mentioned in Week 3 as the common IR substrate but not dissected. (Could be a future advanced elective.)
- **Numerics (FP8/FP4/INT4 quantization)**: mentioned in Week 4 but not a dedicated lecture. Covered in Kaoutar's HPML quantization lecture already.

**Consequences**: if a request touches these, flag the scope boundary and confirm before expanding.

---

## 10. Suggested future artifacts (not yet built)

- **8-layer debugging taxonomy** as a standalone document (for Week 5 + publishable as a blog post or workshop paper)
- **Reading list** per week, in `resources/reading-list.md`
- **Quiz bank** — MCQ questions per week, 15–20 per week, auto-gradable
- **Final portfolio rubric** — cross-lab capstone assessment
- **CI workflow** for nightly notebook smoke-testing

These are tracked as open design work. If you start any of them, update this file.

---

*Last updated: April 2026*
