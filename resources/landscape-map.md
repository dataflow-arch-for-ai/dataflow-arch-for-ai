# Accelerator Landscape Map

A running reference built across the six weeks of the course. **Every week adds rows or cells.** By Week 6 this table contains your working mental model of the five architectures.

The map is not vendor marketing. Each cell is something either observed in lecture or measured in a lab. Shaded rows (marked 🔶) indicate dataflow-first systems.

---

## Week 1 — Execution model, memory hierarchy, programming model

| | **GPU (NVIDIA)** | **TPU (Google)** 🔶 | **Cerebras** 🔶 | **Groq** 🔶 | **Spyre / AIU (IBM)** 🔶 |
|---|---|---|---|---|---|
| **Execution Model** | SIMT / control-flow | Systolic array + dataflow ctrl | Wafer-scale dataflow | Deterministic dataflow | Static dataflow graph |
| **Memory Hierarchy** | L1/L2/HBM cache hierarchy | VMEM + HBM (sw-managed) | 40 GB on-chip SRAM | SRAM-only, no off-chip | Scratchpads + HBM/LPDDR (NUMA-like) |
| **Programming Model** | CUDA / kernels | XLA / HLO graphs | SDK / graph compiler | Groq Compiler / graphs | torch.compile / FX graphs |
| **Compiler Strategy** | TorchInductor / Triton | XLA: fusion, tiling, layout | Whole-model mapping | Deterministic scheduling | Dataflow graph IR, tiling, fusion |
| **Key Tradeoff** | Flexibility vs. efficiency | Throughput vs. programmability | Scale vs. cost | Determinism vs. flexibility | Efficiency vs. op coverage |

> **Lab 1 populates**: measured forward-pass latency, throughput, peak memory, and dominant operator for the GPU column. You add these as additional rows from your own Colab measurements.

---

## Week 2 — Graph representation and scheduling (added in Lab 2)

| | **GPU (NVIDIA)** | **TPU (Google)** 🔶 | **Cerebras** 🔶 | **Groq** 🔶 | **Spyre / AIU (IBM)** 🔶 |
|---|---|---|---|---|---|
| **Graph capture** | torch.fx / TorchDynamo | JAX jit → HLO; TF graph | CS-SDK graph | Groq Compiler graph | torch.compile FX graph |
| **Scheduling granularity** | Kernel-level (fused into CUDA kernels) | Operator-level (HLO ops in systolic loop) | Whole-model placement | Fully deterministic, cycle-accurate | Op-level fused into dataflow IR |
| **Fusion boundaries** | Pointwise + reduction chains | Aggressive; HLO fusion passes | Whole-model | Fixed at compile time | Elementwise + matmul patterns |
| **Data-dependent control** | Supported (graph breaks) | Allowed but costly (recompile) | Restricted | Not supported (determinism) | Restricted; requires fallback |

> **Lab 2 populates**: graph size (nodes/edges), compute-bound vs memory-bound node counts for the GPU column.

---

## Week 3 — Compilation passes (to be added)

Column sketch for Week 3:

| | GPU | TPU 🔶 | Cerebras 🔶 | Groq 🔶 | Spyre 🔶 |
|---|---|---|---|---|---|
| **IR stages** | FX → Inductor IR → Triton | HLO → StableHLO → MLIR | CS-IR | Groq IR | torch-spyre IR → tiles |
| **Fusion strategy** | | | | | |
| **Tiling decision** | | | | | |
| **Layout propagation** | | | | | |

---

## Week 4 — Memory system details (to be added)

| | GPU | TPU 🔶 | Cerebras 🔶 | Groq 🔶 | Spyre 🔶 |
|---|---|---|---|---|---|
| **On-chip capacity** | ~50 MB L2 (H100) | VMEM ~128 MB | 40 GB SRAM | 220 MB SRAM | Scratchpad banks, TBD |
| **HBM bandwidth** | | | *(no HBM)* | *(no HBM)* | |
| **Alignment constraints** | weak | some | strict | strict | "stick memory" alignment |
| **KV cache strategy** | paged attention (vLLM) | HBM management | on-chip | on-chip | scratchpad placement |

---

## Week 5 — Failure modes (to be added)

The **8-layer debugging taxonomy** populates this section. Each row maps to a failure layer; each column records what the signal looks like on that architecture.

---

## Week 6 — Roofline positions (to be added)

Each architecture has a different ridge point (AI where memory-bound transitions to compute-bound). Week 6 lab measures these and plots them.

| | GPU (T4) | TPU v5e 🔶 | Cerebras WSE-3 🔶 | Groq LPU 🔶 | Spyre 🔶 |
|---|---|---|---|---|---|
| **Peak FLOPs (fp16)** | 65 TFLOPS | ~197 TFLOPS | 125 PFLOPS (wafer) | ~750 TFLOPS | TBD |
| **Peak BW (GB/s)** | 320 (GDDR6) | ~819 (HBM3) | on-chip only | on-chip only | TBD |
| **Ridge point AI** | ~10 | ~15 | (no HBM ridge) | (no HBM ridge) | TBD |

---

## How to update this file

After each lab, students should update their *personal copy* of this file with the values they measured. The shared copy in the repo is the reference version.

When updating: **cite your lab run** — include batch size, sequence length, and which Colab GPU you were assigned. Measurements without these are meaningless.

---

*Course: Dataflow Architectures for AI · © 2026 Dr. Kaoutar El Maghraoui*
