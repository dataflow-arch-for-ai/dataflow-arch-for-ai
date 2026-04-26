# Lab 2 — Visualizing Transformer Dataflow

**Week**: 2 — Dataflow Execution Models
**Expected time**: 90–120 minutes
**Runtime**: Google Colab, GPU or CPU (this lab is mostly static analysis)

---

## Learning objectives

By the end of this lab you will be able to:

1. Trace a transformer block into an **FX graph** using `torch.fx.symbolic_trace`.
2. Visualize the resulting computational graph as nodes and edges.
3. Annotate each node with **compute cost (FLOPs)** and **memory footprint (bytes)**.
4. Classify nodes as **compute-bound**, **memory-bound**, or **cheap** using arithmetic intensity.
5. Predict which nodes would become scheduling bottlenecks on a generic dataflow accelerator.

## Why this matters

A dataflow accelerator's compiler does not see your PyTorch code. It sees a graph — nodes (operators), edges (tensors), dependencies. **This lab is the first time you will look at your model the way the hardware does.**

On Spyre, TPU, and Groq, the compiler decides fusion boundaries, tiling, scheduling, and memory placement all from this graph. If you can't read the graph, you can't reason about the hardware.

## Prerequisites

- Lab 1 complete.
- You've completed the Week 2 lecture material on dataflow execution principles and FX graphs.
- Basic familiarity with the transformer architecture (attention, residual connections, FFN).

## Setup

1. Open `starter/lab2_fx_graph_starter.ipynb` in Google Colab.
2. GPU runtime is recommended but not required (this lab is mostly static analysis).
3. Run the first cell; it imports `torch`, `torch.fx`, `networkx`, and `matplotlib` — all pre-installed on Colab.

## Files

```
labs/week-02-fx-graph/
├── README.md                              ← this file
├── starter/
│   └── lab2_fx_graph_starter.ipynb       ← complete this notebook
└── optional_spyre/
    └── lab2_spyre_extension.ipynb        ← optional, requires Spyre access
```

---

## How this lab is graded (100 pts)

| Component | Weight | How it's graded |
|---|---|---|
| **Auto-graded checkpoints** | 60 pts | 4 `Checkpoint` cells (15 pts each). |
| **Peer-reviewed analysis** | 30 pts | 3 written prompts in Part 5. |
| **Landscape Map row** | 10 pts | Week-2 row populated correctly. |

### Auto-graded checkpoints

| # | Verifies |
|---|---|
| Checkpoint 1 | FX trace produces a valid `GraphModule`; op_counts has correct categories. |
| Checkpoint 2 | Networkx graph constructed; node and edge counts consistent. |
| Checkpoint 3 | Every node has `flops` and `bytes` metadata; totals fall in expected range for this block size. |
| Checkpoint 4 | Every node classified; compute/memory/cheap counts sum correctly. |
| Checkpoint 6 | Week-2 Landscape Map row has all required keys with non-`None` values. |

### Peer-review rubric for Part 5

Each of the three written prompts is scored **0–10**, then averaged.

| Prompt | What's being assessed |
|---|---|
| **5.1** (three bottleneck nodes) | Can the student pick specific nodes from their own graph and justify why each matches a dataflow-bottleneck category? |
| **5.2** (fusion opportunity) | Does the student identify a plausible fusable chain and describe what the fused op would read/write? |
| **5.3** (long-sequence QKᵀ calculation) | Does the student correctly distinguish arithmetic intensity from absolute memory pressure? |

**Specificity × correctness rubric** (same structure as Lab 1):

| Axis | 0 pts | 3 pts | 7 pts | 10 pts |
|---|---|---|---|---|
| **Specificity** | No specific node names from the student's own graph | 1 node named | 2 nodes named, partially justified | 3+ nodes named, each justified against a dataflow-bottleneck category |
| **Correctness** | Wrong category or wrong math | Mostly wrong | Correct with minor imprecisions | Correct, shows arithmetic |

**Scoring note**: Prompt 5.3 is the discriminator question. Many students will intuit that longer sequences → more memory pressure → *lower* arithmetic intensity. The correct answer is that AI actually *increases* with S (because compute grows as S² and memory grows with S² plus a linear term). Students who correctly work the arithmetic and flag the distinction between AI and absolute memory pressure are A-grade; students who hand-wave "it's memory-bound because the sequence is longer" score ≤ 5.

---

## Expected output ranges (for TA calibration)

For the default lab parameters (`HIDDEN_DIM=512, NUM_HEADS=8, FFN_DIM=2048, BATCH=2, SEQ=128`):

| Quantity | Expected |
|---|---|
| Total FX graph nodes | ~35–45 |
| `call_module` nodes | 9 (q/k/v/o + ln1/ln2 + ffn_gate/ffn_up/ffn_down) |
| `call_function torch.matmul` nodes | 2 (Q·Kᵀ and attn·V) |
| Total FLOPs | ~2.0–2.4 × 10⁹ (with factor-of-2 matmul) |
| Compute-bound nodes | 5–8 (the Linears and matmuls) |
| Memory-bound nodes | 3–6 (softmax, layernorms, gated multiply) |
| Cheap nodes | remainder (reshapes, transposes) |

Deviation outside these ranges almost always indicates a student error (wrong FLOP formula, double-counting elementwise bytes, etc.).

---

## Submission

When you finish:

1. Verify every **Checkpoint** cell passes.
2. Ensure every `**YOUR ANALYSIS HERE**` block contains your written answer.
3. Run the final `submission.json` cell.
4. **File → Download .ipynb**. Upload both the `.ipynb` and `submission.json` to the course portal.

---

## Common pitfalls

- **Trying to trace full `transformers` models**: raises `Proxy has no attribute ...` errors because modern LLMs have data-dependent control flow. Use the provided `TinyTransformerBlock` — it mirrors Qwen's structure while remaining fx-traceable. Week 3 covers `torch.export` and `torch.compile` Dynamo for tracing full models.
- **Skipping `ShapeProp`**: without it, `node.meta["tensor_meta"]` doesn't exist and the FLOP computation fails silently (returns 0 for everything). The solution path calls `ShapeProp(gm).propagate(dummy)` before annotating.
- **FLOP factor-of-2 error**: matmul FLOPs = `2 * M * N * K` (one multiply + one add per output element). Checkpoint 3 catches a 50% error.
- **Double-counting bytes**: each tensor should be counted once per operator it touches (as input) and once when it's produced (as output). Our helpers already handle this.
- **Graphviz not available**: the starter falls back to `nx.spring_layout`. The visualization is uglier but the checkpoints still pass.

---

## Optional — Dataflow Deep Dive (Spyre hardware required)

Open `optional_spyre/lab2_spyre_extension.ipynb` to compile this same block with the Spyre compiler, dump its internal dataflow IR, and catalog what the compiler **changed** from your FX graph (fused ops, reordered nodes, inserted data-movement edges). You will compare its fusion decisions directly against your predictions in Part 5.2.

Spyre extension adds:
- 10 pts: Spyre IR captured and diff'd against FX graph
- 10 pts: fusion-prediction comparison paragraph

Total possible with extension: 120/100.

---

## Connecting to Week 3

The graph you built in this lab is exactly the input to a dataflow compiler. Week 3 will take the same graph and show what XLA (for TPU), TorchInductor (for GPU), and the Spyre compiler *do* to it — fusion, tiling, layout propagation, scheduling. The FX graph here is the input; the compiled executable is the output.

---

*Course: Dataflow Architectures for AI — Week 2 · v1.0 · © 2026 Dr. Kaoutar El Maghraoui*
