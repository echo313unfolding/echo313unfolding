<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0d1117,2a2a2a&height=200&section=header&text=echo313unfolding&fontSize=42&fontColor=c9d1d9&fontAlignY=35&desc=Auditable%20AI%20runtime%20infrastructure&descSize=16&descColor=8b949e&descAlignY=55" width="100%" />

**Auditable AI runtime infrastructure.**
**Every claim has a receipt.**

</div>

## Projects

### Auditable AI Runtime

| Repo | What | Status |
|------|------|--------|
| **[helix-substrate](https://github.com/echo313unfolding/helix-substrate)** | Calibration-free HXQ weight compression | v0.3.3 on [PyPI](https://pypi.org/project/helix-substrate/), 15 models on [HF](https://huggingface.co/EchoLabs33) |
| **[sentinel-hybrid-stack](https://github.com/echo313unfolding/sentinel-hybrid-stack)** | External recurrent memory + post-LLM safety gate | v0.1.0, 60/60 tests, no LLM required |
| **[helix-online-kv](https://github.com/echo313unfolding/helix-online-kv)** | KV cache compression using same VQ codec | 1.9x more context, 87.5% attention sparsity |
| **[echo_runtime](https://github.com/echo313unfolding/echo_runtime)** | Unified compressed inference runtime | All layers in one forward pass |

**helix-substrate** compresses any `nn.Linear` across Transformers, SSMs, hybrids, MoEs, and vision models. Calibration-free. ~2x from BF16, ~4x per-tensor from FP32.

**sentinel-hybrid-stack** demonstrates how deterministic safety layers can augment LLM-based triage: a recurrent SSM tracks entity state, and a conservative gate catches dangerous contradictions the model misses.

Same design principles: model-pluggable, receipts-first, audit-friendly.

### Forensic Graph Intelligence

| Repo | What | Status |
|------|------|--------|
| **[FGIP](https://github.com/echo313unfolding/FGIP)** | Graph intelligence from government data | 1,801 nodes, 3,286 edges, adversarial-tested |

Tracks lobbying, ownership, and judicial networks from government data (SEC EDGAR, FEC, Congress.gov, Federal Register, FRED). Every thesis is adversarial-tested with control groups.

### What we do not claim

- HXQ does not beat every quantizer on speed or compression ratio. Its advantages are calibration-free operation, architecture breadth, and quality.
- Sentinel is an educational reference implementation, not production SOC software.
- Not every model works equally well. We publish the numbers, including the weak ones.

## Methodology

- **Receipts or it didn't happen** — every claim backed by verifiable JSON receipts with cost blocks
- **Adversarial-first** — find the strongest counter-argument before claiming verification
- **Real data only** — synthetic test data is flagged, not trusted
- **Proof layers**: (1) code runs, (2) correct output format, (3) real data, (4) statistical significance

## License

Echo Labs LLC. sentinel-hybrid-stack is MIT.

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0d1117,2a2a2a&height=120&section=footer" width="100%" />

</div>
