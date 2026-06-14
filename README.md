# echo313unfolding

**Research question:** What structure survives lossy compression of neural network weights, and what can you do with it without decompressing?

**Core finding (experimentally supported, 217 tests across 15 suites):**
Transition structure appears to preserve execution-relevant behavior after value-level identity is compressed away. Three invariants — transition diversity, memory length, spatial coherence — form a minimum behavioral fingerprint that predicts role, architecture family, and execution properties (R² = 0.818 on output norm) from the compressed representation alone.

This is not a claim about lossless reconstruction. It is a claim about what the compressed artifact *already encodes* — enough to route, classify, and pre-screen without materializing the original weights.

---

## Research Threads

### 1. HXQ — Calibration-Free Compression and Compressed-Domain Compute

Scalar k-means VQ with per-group affine correction. No calibration data. Works on transformers, SSMs, hybrids, MoEs, vision models (15 architectures tested). The interesting part is not the compression ratio — it's that the compressed form is directly executable via gather-matmul (no decompression step).

| Repo | What | Evidence |
|------|------|----------|
| [helix-substrate](https://github.com/echo313unfolding/helix-substrate) | Python quantizer + HuggingFace integration | v0.3.3, 15 models on [HF](https://huggingface.co/EchoLabs33), cos >= 0.999 |
| [helix-codec](https://github.com/echo313unfolding/helix-codec) | Standalone C99 codec library | Non-LLM proof: 1024 embeddings cos_min 0.9996, MIT |
| [hxq-native](https://github.com/echo313unfolding/hxq-native) | C/CUDA decompression kernels | GPU decode 27.83 tok/s = Q8_0 parity at 26% less size |
| [hxq-whitepaper](https://github.com/echo313unfolding/hxq-whitepaper) | Technical paper | v3, methodology + results |
| [llama.cpp fork](https://github.com/echo313unfolding/llama.cpp) | GGML_TYPE_HXQ integration | `hxq-affine-type` branch, 10 files, native mmvq kernel |
| [helix-online-kv](https://github.com/echo313unfolding/helix-online-kv) | KV cache compression (PolarQuant) | Gate PASS: 55-59% MSE improvement, research prototype |

**Key results:**
- 3090 decode: 226.53 tok/s (92.4% of Q4_K_M, beats Q6_K by 10.6%)
- Architecture breadth: Qwen, Llama, Mamba, Zamba2, OLMoE, CLIP, SBERT, ResNet
- Non-ML tensor distributions pass (Cauchy kurtosis 5765: cos 0.9985)
- Fractal IFS codebook: 6.7x codebook compression with recursive Barnsley maps (real weights, PASS)

**What is not claimed:** HXQ does not beat Q4_K_M on speed. Its contribution is calibration-free operation, architecture breadth, and the compressed-domain compute path.

---

### 2. Crystal Vault / Ghost Coordinates — Behavioral Fingerprinting from Compressed Artifacts

The experimental program asking: what can you learn about a tensor's *role and behavior* from its compressed representation, without decompressing it?

**Status:** Active research, 217 tests across 15 suites (Phases 0–0.19). Code not yet public — results documented in test receipts.

**Proven (by test):**
- Ghost classifier: k-NN on 292 real tensors. Role accuracy 73.3% (8.1x random baseline), architecture family 92.8%. ([Phase 0.15](https://github.com/echo313unfolding/helix-substrate))
- Execution prediction: Ghost features beat shape-only on all 6 execution targets. Best: output_norm R² = 0.818 vs shape R² = 0.238. ([Phase 0.16])
- Pre-route decision: Architecture-aware multi-feature routing clears 53.8% of tensors with precision 0.955, recall 0.904. ([Phase 0.17b])
- Minimum invariant basis: TE + MO + AC confirmed — dropping any one degrades. Every feature contributes. ([Phase 0.18])

**Active hypothesis:**
- Cross-architecture generalization is family-specific, not universal. Within-family transfers (Mamba → Mamba2: 88.5%). Cross-family fails. ([Phase 0.19])

**Speculative:**
- Whether ghost coordinates constitute a substrate-independent behavioral description (Level 4 claim — too strong, not proven)

---

### 3. MorphSAT — Structured Cognitive Control for LLM Agents

**When should an LLM agent stop gathering evidence and commit to a decision?**

MorphSAT answers this by embedding the model inside an external control structure that holds decision authority. The model proposes actions; a shadow monitor with posture states (ORIENT, SAFE_DISTANCE, INVESTIGATING, COMMIT_READY, ESCALATE_READY) decides when to commit. Nine versions tested, each falsifying or confirming a specific mechanism.

| Repo | What | Evidence |
|------|------|----------|
| [MorphSAT](https://github.com/echo313unfolding/MorphSAT) | Full implementation + benchmark | 100% accuracy (v8.3), 123 tests, MIT |

**Key results:**
- v8.3: 100% on 20-scenario benchmark (gate_assists + early-verdict guard)
- Dual-agent recomputation gate: 100% precision on disagreement detection
- Nine-version proof chain: each version tests one mechanism, records what failed and why
- Novelty-as-penalty DEAD (v6). Novelty-as-posture WORKS (v7): benign accuracy 35.7% → 78.6%

**For cognitive architecture reviewers:**
- [`docs/COGNITIVE_ARCHITECTURE_TRANSLATION.md`](https://github.com/echo313unfolding/MorphSAT/blob/main/docs/COGNITIVE_ARCHITECTURE_TRANSLATION.md) — term mapping to Soar, ACT-R, and active inference
- [`docs/morphsat_technical_note.md`](https://github.com/echo313unfolding/MorphSAT/blob/main/docs/morphsat_technical_note.md) — 2-page technical note with full results
- Shadow monitor maps to Soar's operator selection + metacognitive control
- Split memory (threat/tolerance stores) maps to episodic memory with bidirectional familiarity
- Swarm escalation maps to impasse/substate handling
- The core claim: structured external control compensates for a model operating outside its primary domain (7B coding model achieving 100% on security triage)

---

### 4. Cell Runtime — Multi-Model Orchestration

Single-GPU multi-model routing with specialist cartridges, hardware-aware swapping, and rolling session memory.

| Repo | What | Evidence |
|------|------|----------|
| [cell-runtime](https://github.com/echo313unfolding/cell-runtime) | Orchestrator + gateway + memory lane | Spec + implementation, 153 tests |
| [echo-sentry](https://github.com/echo313unfolding/echo-sentry) | External recurrent memory + safety gate | v0.1.0, 60 tests, MIT |

---

### 5. FGIP — Evidence Graph Intelligence

Maps federal obligations, tariffs, mineral supply chains, donor networks, and reshoring flows from government source documents. Every edge has a source citation. AI-generated claims enter as UNVERIFIED (tier-3) and require fetched-URL verification receipts for promotion.

| Repo | What | Evidence |
|------|------|----------|
| [FGIP](https://github.com/echo313unfolding/FGIP) | Graph database + 24 agents | 2,100+ nodes, 2,800+ edges, 69K+ proposed |
| [fgip-globe](https://github.com/echo313unfolding/fgip-globe) | CesiumJS 3D visualization | Geospatial rendering of funding chains |

**Proven findings:** Energy intensity explains tariff gradient (r = 0.709, p < 0.01). Sanctions evasion as formula DISPROVEN (r = 0.016). 5GW citation chain mapped into SCOTUS amicus record.

---

### 6. HXQ-Solana — Receipt-Gated Asset Provenance

Protocol-level quality enforcement for off-chain artifacts. Token-2022 Transfer Hook blocks transfers if compressed asset fails fidelity gate. Not "AI on blockchain" — provenance infrastructure where quality is self-enforcing at the protocol layer.

| Repo | What | Evidence |
|------|------|----------|
| [hxq-solana](https://github.com/echo313unfolding/hxq-solana) | Solana program + Transfer Hook | Devnet proven: active ALLOWED, quarantined BLOCKED |

---

## For Cognitive Architecture Reviewers

If you study cognitive architectures, structured agency, or substrate-independent computation:

1. **Start here:** [MorphSAT — Cognitive Architecture Translation](https://github.com/echo313unfolding/MorphSAT/blob/main/docs/COGNITIVE_ARCHITECTURE_TRANSLATION.md)
   - Direct term mapping: MorphSAT → Soar, ACT-R, active inference
   - The research question is operator selection under uncertainty with bounded resources

2. **Then:** [MorphSAT — Technical Note](https://github.com/echo313unfolding/MorphSAT/blob/main/docs/morphsat_technical_note.md)
   - 2-page summary with experimental results
   - Nine-version ablation chain showing which control mechanisms work and which fail

3. **The compression connection:** Crystal Vault / Ghost coordinates ask whether behavioral properties of neural computation survive compression — whether you can characterize what a tensor *does* from its compressed form alone. If yes, this is a substrate-independent behavioral description derived from statistical structure rather than value-level identity.

4. **The connecting claim (hypothesis, not proven):** The same structural invariants that allow pre-routing in a compressed runtime may correspond to the kind of functional characterization that cognitive architectures use to describe processing modules — role, capacity, interaction pattern — independent of implementation substrate.

---

## Methodology

- **Receipts or it didn't happen** — every claim backed by JSON receipts with compute cost blocks
- **Proof layers:** (1) code runs, (2) correct output format, (3) real data, (4) statistical significance
- **Falsification-first** — each version of MorphSAT records what failed and why; dead paths are documented alongside working ones
- **Tier-gated evidence** — AI-generated claims cannot self-promote; verification requires fetched URLs and content hashes

## What this is not

- Not a startup. No product landing page.
- Not all proven. Active hypotheses and speculative connections are marked as such.
- Not claiming novelty without receipts. The test counts are real. The dead ends are documented.

---

Echo Labs LLC | [HuggingFace models](https://huggingface.co/EchoLabs33) | MIT where marked
