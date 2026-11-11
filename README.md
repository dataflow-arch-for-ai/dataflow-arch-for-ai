# Dataflow Architectures for AI

Public course materials for *Dataflow Architectures for AI: From Principles to Practice*, a 6-week course on reasoning across AI accelerator architectures (GPU, TPU, Cerebras, Groq, IBM Spyre) using dataflow execution as the common lens.

**Instructor:** Dr. Kaoutar El Maghraoui, Principal Research Scientist, IBM Research; Adjunct Professor of Computer Science, Columbia University

This repository holds the student-facing labs and reference material. Solution notebooks, grading keys, and quiz answers are kept in a separate private repository and are not published here.

## Quick start

Every lab runs on Google Colab with a free-tier GPU (T4). No proprietary hardware is required. Open a lab by clicking its badge:

| Week | Topic | Lab | Open |
|------|-------|-----|------|
| 1 | The Accelerator Landscape | Profiling LLM Inference Across Hardware | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dataflow-arch-for-ai/dataflow-arch-for-ai/blob/main/gpu-labs/student/Lab1_Profiling_Inference_STUDENT.ipynb) |
| 2 | Dataflow Execution Models | Visualizing Transformer Dataflow | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dataflow-arch-for-ai/dataflow-arch-for-ai/blob/main/gpu-labs/student/Lab2_Transformer_Dataflow_STUDENT.ipynb) |
| 3 | Compilation for Dataflow | Compiling Models for Dataflow Execution | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dataflow-arch-for-ai/dataflow-arch-for-ai/blob/main/gpu-labs/student/Lab3_Compiling_for_Dataflow_STUDENT.ipynb) |
| 4 | Memory Systems | Profiling Memory Access Patterns in LLMs | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dataflow-arch-for-ai/dataflow-arch-for-ai/blob/main/gpu-labs/student/Lab4_Profiling_Memory_Access_STUDENT.ipynb) |
| 5 | Debugging Across the Dataflow Stack | Debugging the Compilation Stack | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dataflow-arch-for-ai/dataflow-arch-for-ai/blob/main/gpu-labs/student/Lab5_Debugging_Compilation_Stack_STUDENT.ipynb) |
| 6 | Performance Modeling | Roofline Analysis for LLM Inference | [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/dataflow-arch-for-ai/dataflow-arch-for-ai/blob/main/gpu-labs/student/Lab6_Roofline_Analysis_STUDENT.ipynb) |

Set the runtime before running a lab: in Colab, **Runtime > Change runtime type > T4 GPU**. The first cell installs dependencies and checks that a GPU is visible.

All labs use `Qwen/Qwen2.5-0.5B-Instruct` (Lab 4 also uses `distilgpt2`). Both are public models that download automatically; no access tokens are needed.

## How the labs work

Each notebook follows the same structure:

1. An environment check that fails early if the GPU runtime is missing.
2. Numbered parts with `TODO` markers for you to complete.
3. `Checkpoint` cells with `assert` statements that must pass (no `AssertionError`) once your code is correct.
4. A row you add to the Accelerator Landscape Map from your own measurements.
5. A results cell that writes a JSON file.

To get feedback on a lab, run the final results cell, copy the JSON it prints into the grader notebook on Coursera Labs, and run that notebook. The grader reports self-assessment points. Those points are feedback on how the lab went, not a grade. The graded item each week is the Concept Check quiz on Coursera.

Do not edit the `Checkpoint` cells or the results cell. They define how the lab is checked.

## Repository structure

```
dataflow-arch-for-ai/
├── README.md
├── gpu-labs/
│   └── student/                 # the six labs, one per week
│       ├── Lab1_Profiling_Inference_STUDENT.ipynb
│       ├── Lab2_Transformer_Dataflow_STUDENT.ipynb
│       ├── Lab3_Compiling_for_Dataflow_STUDENT.ipynb
│       ├── Lab4_Profiling_Memory_Access_STUDENT.ipynb
│       ├── Lab5_Debugging_Compilation_Stack_STUDENT.ipynb
│       └── Lab6_Roofline_Analysis_STUDENT.ipynb
└── resources/
    └── landscape-map.md         # the running reference table, extended each week
```

## Accelerator Landscape Map

`resources/landscape-map.md` is a reference table built across the six weeks. Each lab adds a row or cell from values you measure, not from vendor specifications. Keep your own copy and record the batch size, sequence length, and Colab GPU for every measurement, since numbers without that context are not comparable.

## Solutions

Solution notebooks, grading rubrics, and quiz answers live in a separate private repository, `dataflow-arch-for-ai-solutions`. They are not in this public repo and are not reachable from it. Instructors and TAs can request access through the IBM Research academic partnership program.

## Teaching staff

- Instructor and course author: Dr. Kaoutar El Maghraoui, Principal Research Scientist, IBM Research; Adjunct Professor of Computer Science, Columbia University
- Lab instructor: Dr. Rashed Z. Bhatti, IBM Research (contributor to the lab notebooks)

## License

- Course materials (README, reference tables, written prompts): CC-BY-4.0
- Code (notebooks, scripts): MIT

## Citation

```
El Maghraoui, K. (2026). Dataflow Architectures for AI: From Principles to Practice.
IBM Research.
https://github.com/dataflow-arch-for-ai/dataflow-arch-for-ai
```

## Contact

Use GitHub Issues for course-related questions. For Spyre hardware access or institutional partnerships, contact the IBM Research academic partnerships program.

© 2026 Dr. Kaoutar El Maghraoui. Developed with IBM Research.
