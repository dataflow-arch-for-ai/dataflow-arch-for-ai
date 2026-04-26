# Dataflow Architectures for AI — Course Materials

**Instructor**: Dr. Kaoutar El Maghraoui — Principal Research Scientist, IBM Research; Adjunct Professor of Computer Science, Columbia University

This repository contains the public course materials for **Dataflow Architectures for AI: From Principles to Practice** — a 6-week professional course on reasoning across AI accelerator architectures (GPU, TPU, Cerebras, Groq, IBM Spyre) using dataflow execution as the unifying lens.

---

## Quick start

All core labs run on **Google Colab** with free-tier GPU runtime. No proprietary hardware access required.

| Week | Lab | Open in Colab |
|------|-----|---|
| 1 | Profiling LLM Inference Across Hardware | `labs/week-01-profiling/starter/lab1_profiling_starter.ipynb` |
| 2 | Visualizing Transformer Dataflow | `labs/week-02-fx-graph/starter/lab2_fx_graph_starter.ipynb` |
| 3 | *(Coming soon)* Exploring Compilation Pipelines | — |
| 4 | *(Coming soon)* Profiling Memory Access Patterns | — |
| 5 | *(Coming soon)* Debugging LLM Failures | — |
| 6 | *(Coming soon)* Applying Roofline Analysis | — |

> **Note**: Colab "Open in Colab" badge URLs are added once the repo is pushed to GitHub. Replace `REPLACE-WITH-GITHUB-URL` in this README with your actual URL.

---

## How to use this repository

### For students
1. Fork or clone the repo.
2. Each week: open the lab in `labs/week-NN-*/starter/` in Colab.
3. Complete the `TODO` markers. Every **Checkpoint** cell must pass (no `AssertionError`).
4. Fill in every `**YOUR ANALYSIS HERE**` section — those are peer-reviewed.
5. Run the final `submission.json` cell and upload the resulting file through the course portal.

### For instructors / TAs
Solution notebooks, grading rubrics, and "common mistakes" guides live in the separate **`dataflow-arch-for-ai-solutions`** private repository. Request access via the IBM Research academic partnership program.

---

## Course design

A few principles shape this course:

- **Dataflow-first, vendor-second.** Every concept introduced as a general principle before any specific system is named. Spyre, TPU, Cerebras, and Groq appear as comparison points, never the subject.
- **Comparative throughout.** Each week includes side-by-side analysis across at least three architectures. The *Accelerator Landscape Map* (see `resources/landscape-map.md`) is a running artifact that grows across all six weeks.
- **Code in every module.** Core labs run 90–120 minutes. Students write and run real code.
- **Two-tier labs.** Core labs are mandatory and run on Colab. Optional **Dataflow Deep Dive** sections extend the same work to Spyre hardware for students in partnered programs.
- **Open-source only in the required path.** Every tool in the core labs is open-source and works anywhere.

---

## Repository structure

```
dataflow-arch-for-ai/
├── README.md                              # this file
├── SYLLABUS.md                            # polished course description
├── LICENSE.md                             # CC-BY-4.0 for materials, MIT for code
├── .github/workflows/
│   └── smoke-test-notebooks.yml           # CI: runs every student notebook nightly
│
├── lectures/                              # slides, notes, readings per week
│   ├── week-01-landscape/
│   ├── week-02-dataflow/
│   └── ...
│
├── labs/                                  # Colab-ready notebooks
│   ├── week-01-profiling/
│   │   ├── README.md                      # lab objectives, grading, submission
│   │   ├── starter/
│   │   │   └── lab1_profiling_starter.ipynb
│   │   └── optional_spyre/
│   │       └── lab1_spyre_extension.ipynb
│   ├── week-02-fx-graph/
│   └── ...
│
├── resources/
│   ├── landscape-map.md                   # evolving reference table, updated each week
│   ├── debugging-taxonomy.md              # the 8-layer model (published in Week 5)
│   ├── reading-list.md
│   └── glossary.md
│
├── assessments/
│   ├── quiz-week-1.md
│   ├── ...
│   └── final-portfolio.md                 # cross-lab capstone rubric
│
└── docs/
    ├── getting-started.md
    ├── colab-setup.md                     # GPU runtime, quota tips, common errors
    └── contributing.md
```

**Solutions live in a separate private repository** (`dataflow-arch-for-ai-solutions`), not in this public repo. This is intentional: GitHub branch protection prevents writes but not reads, so a "solutions" branch on a public repo would still be publicly visible. Two separate repositories with independent access control are the only way to actually hide solution content from students.

---

## Citation

If you use or adapt these materials for your own teaching, please cite:

```
El Maghraoui, K. (2026). Dataflow Architectures for AI: From Principles to Practice.
IBM Research / Columbia University. https://github.com/REPLACE-WITH-GITHUB-URL
```

A `CITATION.cff` file is included for automated citation tools.

---

## License

- **Course materials** (lecture notes, slide decks, README files, rubrics): **CC-BY-4.0**
- **Code** (notebooks, scripts): **MIT License**

See `LICENSE.md` for full text.

---

## Contact

For course-related questions: use GitHub Issues.
For Spyre hardware access or institutional partnerships: IBM Research academic partnerships program.

---

*© 2026 Dr. Kaoutar El Maghraoui. Materials developed in collaboration with IBM Research and Columbia University Fu Foundation School of Engineering and Applied Science.*
