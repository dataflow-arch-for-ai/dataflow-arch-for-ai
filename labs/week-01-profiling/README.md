# Lab 1 — Profiling LLM Inference Across Hardware

**Week**: 1 — The Accelerator Landscape
**Expected time**: 90–120 minutes
**Runtime**: Google Colab, GPU (T4 or better)

---

## Learning objectives

By the end of this lab you will be able to:

1. Build a repeatable benchmarking harness using `torch.utils.benchmark.Timer` that measures inference latency and throughput for an LLM on CPU and GPU.
2. Profile memory allocation patterns during inference using `torch.profiler` and `torch.cuda.memory_allocated`.
3. Construct the first row of the course *Accelerator Landscape Map* from your own measurements, not vendor slides.
4. Explain the measured CPU vs GPU performance gap in terms of specific architectural features (SIMT parallelism, HBM bandwidth, cache hierarchy).

## Prerequisites

- Intermediate Python; comfort with PyTorch `nn.Module` and tensor operations.
- You've completed the Week 1 lecture material on control-flow vs dataflow execution and the Accelerator Landscape Map.
- A Google account for Colab access.

## Setup

1. Open `starter/lab1_profiling_starter.ipynb` in Google Colab.
2. In Colab: **Runtime → Change runtime type → T4 GPU** (free tier is sufficient).
3. Run the first cell; it installs `transformers` and `accelerate` and verifies the GPU is visible.

## Files

```
labs/week-01-profiling/
├── README.md                                  ← this file
├── starter/
│   └── lab1_profiling_starter.ipynb          ← complete this notebook
└── optional_spyre/
    └── lab1_spyre_extension.ipynb            ← optional, requires Spyre access
```

The **solution notebook** lives on the `solutions` branch (instructor/TA access only).

---

## How this lab is graded (100 pts)

| Component | Weight | How it's graded |
|---|---|---|
| **Auto-graded checkpoints** | 60 pts | 4 `Checkpoint` cells (15 pts each). Each must pass without `AssertionError`. |
| **Peer-reviewed analysis** | 30 pts | 3 written prompts in Part 5, graded by peers against the rubric below. |
| **Landscape Map row** | 10 pts | Populated correctly in Part 4. Auto-checked for completeness + sanity-checked by peers for content quality. |

### Auto-graded checkpoints

| # | Verifies |
|---|---|
| Checkpoint 1 | `bench_forward()` returns a sensible median latency on a reference module. |
| Checkpoint 2 | CPU/GPU measurements fall in plausible ranges; speedup > 1.5× (GPU faster). |
| Checkpoint 3 | Peak memory is ≥ model weights; top-3 operators extracted as `(name, time_us)` tuples. |
| Checkpoint 4 | Landscape Map row has all 8 required keys with non-`None` values. |

### Peer-review rubric for Part 5 analysis

Each of the three written prompts is scored **0–10** on two axes, then averaged.

| Axis | 0 pts | 3 pts | 7 pts | 10 pts |
|---|---|---|---|---|
| **Specificity** | No concrete architectural features named | 1 feature named, vaguely | 2–3 features, partially explained | 3+ features, each tied to the mechanism |
| **Correctness** | Factually wrong | Mostly wrong but some valid points | Correct with minor imprecisions | Correct and quantitatively grounded |

**Scoring shortcut for peer reviewers**: students who cite their own measured numbers from Parts 1–3 automatically score ≥ 7 on specificity. Students who use vague language ("GPUs are faster because of parallelism") without naming mechanisms score ≤ 3.

**Final prompt score** = `(specificity + correctness) / 2`, rounded to nearest integer.

---

## Submission

When you finish:

1. Verify every **Checkpoint** cell passes (no `AssertionError`).
2. Ensure every `**YOUR ANALYSIS HERE**` block contains your written answer.
3. Run the final `submission.json` cell. It produces `submission.json` in the working directory.
4. In Colab: **File → Download .ipynb**. Download the notebook AND `submission.json`.
5. Upload both files through the course portal.

---

## Common pitfalls

- **Using `time.time()` instead of `Timer`**: CUDA calls are asynchronous; naive timing measures kernel launch, not execution. Always use `torch.utils.benchmark.Timer` or pair `time.time()` with `torch.cuda.synchronize()`.
- **Skipping warm-up**: the first 1–3 forward passes include JIT compilation and cache warming. Discard them.
- **Confusing `max_memory_allocated` with `max_memory_reserved`**: the former is what your forward actually needed; the latter includes the PyTorch caching allocator's slack.
- **Speedup < 2×**: Colab occasionally assigns older GPUs. If you see this, note it in your analysis — it's not a bug; it's data.
- **CPU model runs out of RAM**: if the Colab CPU runtime OOMs loading fp32 Qwen (~2 GB), restart the runtime and try loading in bf16 instead.

---

## Optional — Dataflow Deep Dive (Spyre hardware required)

If you have Spyre access: open `optional_spyre/lab1_spyre_extension.ipynb` and run the same harness on Spyre hardware. Extend your `landscape_row` into a second row for Spyre, and write a **4th analysis paragraph** comparing where Spyre's static dataflow model produces materially different numbers from your GPU run.

Spyre extension adds:
- 10 pts: Spyre measurements collected
- 10 pts: comparative analysis paragraph

These are bonus points; total possible for the lab becomes 120/100. Does not displace core-lab points.

---

*Course: Dataflow Architectures for AI — Week 1 · v1.0 · © 2026 Dr. Kaoutar El Maghraoui*
