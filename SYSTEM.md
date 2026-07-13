# SYSTEM.md — Echo Labs architecture

> **A local-first agent compute substrate that turns heterogeneous data and models into
> bounded artifacts, profiles them before expensive computation, routes them through
> constrained runtimes, gates execution, and records consequential transformations as
> verifiable evidence.**

This is the whole-system map. Per-repo status (SHAs, test state, gaps) lives in
[`SYSTEM_STATUS.yaml`](./SYSTEM_STATUS.yaml); this document is the architecture the
individual repositories are components of.

**What this is not (yet):** a complete operating system, a production data market, a
finished rights protocol, or a universal ontology. Several layers are proven components
that are **not yet composed into one strict end-to-end path** — that missing composition
is stated plainly below, not hidden.

## The recurring primitive

The same shape appears at every layer — compression, memory, routing, safety, evidence,
settlement:

```
PROFILE   what is this artifact / task / state?
ROUTE     what is the cheapest sufficient and permitted path?
EXECUTE   perform a bounded operation
RECEIPT   record exactly what was decided and produced
GRAPH     connect the result to prior evidence and future decisions
```

## Canonical flow

```
source / input
  → canonical artifact
  → HXQ encode
  → artifact / state profile
  → route decision
  → typed plan IR
  → safety / policy gate
  → local execution
  → deterministic receipt
  → evidence graph
  → optional provenance / settlement adapter
```

## Maturity vocabulary (used below and in the manifest)

`proven component` · `proven interface` · `thin adapter` (interface only, not a real
runtime call) · `strict integration` (real end-to-end) · `mock / fixture` · `hypothesis`
· `historical lineage`.

## Layers

### Artifact formation
**Purpose:** turn model weights / signals into portable, independently reproducible
artifacts. **Two related but DISTINCT HXQ codec families (different algorithms — do not
conflate):**
- **HXQ-VQ / CDNAv3** (k-means VQ + sidecar): `helix-substrate` (Python reference, format-defining) ↔ `hxq-native` (C/CUDA companion).
- **HXQ-Affine** (per-group min/max affine blocks, no codebook): `helix-codec` (C99) ↔ `llama.cpp` HXQ affine GGML types.
- Spec: `hxq-whitepaper`.

**In:** dense tensors / HF models. **Out:** HXQ artifact + fidelity receipt. **Evidence:**
Zamba2-1.2B 136/136 modules at max_error 0.0 (`hxq-native`, VQ family); non-LLM embeddings
cos 0.999676, C-vs-Python bit-identical (`helix-codec`, affine family); GGUF `Q8_0` parity.
**Integration:** **multiple proven implementations across the two families, sharing a
common artifact/receipt *direction* — but canonical interoperability (one byte format +
golden vectors) remains PENDING.** Not yet one proven cross-implementation format.
**Gap:** cross-implementation byte vector + family-bridge not done; `helix-substrate`
package import mid-refactor.

### Runtime-state compression
**Purpose:** compress KV-cache / activations at inference time. **Repo:**
`helix-online-kv`. **Evidence:** CDC-03 attention cos 0.99973 on 252 real entries.
**Integration:** strong prototype, not wired into a runtime path. **Gap:** speedups are
projected op-counts; drop-in cache does not yet free VRAM.

### Artifact profiling & routing
**Purpose:** read what a tensor *does* from its compressed geometry, before running it.
**Repo:** `echo-origin-gold` (private). **Evidence:** 60.9% role accuracy (5.5× random,
leave-one-layer-out) on **one** 167M model; 63.7% on pretrained. **Integration:** thin
adapters (interface proven; not real `cell-runtime` calls). **Gap:** unvendored
`ghost_bridge`; no cross-architecture generalization. *(The 95.3% figure is in-sample —
not featured.)*

### Plan & policy IR
**Purpose:** lower NL / model output into typed, validated intermediate representation
before execution; compile intent through capability gates. **Repos:** `poetica`,
`KRISPER`, and the `cell-runtime` plan-IR (typed IR, constrained-decoding grammar, six
lowering targets, execution oracle). **Out:** typed plan-IR + deterministic hashed receipt
(hashed audit record — **not** cryptographically signed unless the home box confirms a
signing key). **Integration:** real compiler/validation work; **KRISPER not yet called by
the orchestrator.**

### Local agent runtime
**Purpose:** route work across local models, execute bounded actions, keep session
state. **Repo:** `cell-runtime`. **Evidence:** router + OpenAI-compatible gateway +
model-agnostic state/gate separation. **Integration:** production (partial). **Gap:**
bypasses the published `echo-sentry` library; default config collapses lanes.

### Safety / control
**Purpose:** the model proposes; an external gate decides continue/commit/abstain/
escalate. **Repos:** `MorphSAT` (FSA + posture machine + dual-agent verifier, reproducible
receipts), `echo-sentry` (deterministic SSM + post-LLM gate core). **Integration:**
`MorphSAT` is a proven component **not instantiated in the production runtime path**;
`echo-sentry`'s public repo is the decision core (its README is being corrected to match
the checkout). **Gap:** the safety layer is not yet in the runtime loop.

### Evidence / ontology
**Purpose:** a domain-agnostic graph of claims, sources, provenance receipts, and a gated
proposal→promotion write path. **Repos:** `FGIP` (generic core under a finance skin; a
second `security` domain proves genericity), `fgip-globe` (read-only view). **Gap:** MCP
hardcodes the finance domain; agent-substrate lookup is minimal.

### Provenance / settlement
**Purpose:** bind an on-chain transfer to the verified state of an off-chain artifact.
**Repo:** `hxq-solana`. **Evidence:** Token-2022 transfer hook — Active ALLOWED,
quarantined BLOCKED, on devnet. **Integration:** strict demo (bounded). **Scope:** one
working settlement adapter — **not** the general rights economy.

### Personal-data receipts
**Purpose:** local-first custody + tamper-evident receipt chain. **Repo:** `almanac-core`.
**Evidence:** AES-256-GCM vault, key rotation, exports commitments not PII. **Gap:**
broader licensing/market not built; not imported by the field demo.

### Applications
`almanac-field-signal-demo` (cross-stack **narrator** — proves interface shape + receipt
chain; several stages mocked/fixture), `mcelroy-inventory` (a real business MCP app —
proof the pattern carries an ordinary domain pack; not integrated with the substrate).

### Historical lineage (not a dependency)
`helix-cdc` — an earlier DNA/seed-regeneration research line whose headline claim is
disproven by its own docs. Kept for lineage; **not** a current production dependency.

## The honest status in one line

Many **proven components** and several **proven interfaces/adapters**; **not yet one
strict, production-like composition of Layers 1–6.** Building that single no-fallback
reference path is the critical work — see `SYSTEM_STATUS.yaml → genuine_build_gaps`.
