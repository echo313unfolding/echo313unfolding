# Echo Labs

I build inference software for GPUs with limited memory. On my 4 GB Quadro T2000, Qwen2.5-Coder-3B-Instruct generated **37.0 tokens/s** with HXQ affine-6 (AF6) during a 128-token generation test in October 2026. The same build processed a 512-token prompt at **241.6 tokens/s**. [Qwen T2000 benchmark](https://github.com/echo313unfolding/helix-substrate/blob/2a951fd3044a6d142e62f8e3015cd8a49a301c4c/receipts/hxq_af6_probe_qwen3b/MODEL_ZOO_REGRESSION_MATRIX_v3.md#L372-L374)

## Generating tokens from packed weights

AF6 stores a scale, an offset and 128 six-bit weights per group. During single-token generation, my llama.cpp fork reads four weight values from three packed bytes, unpacks them in GPU registers and accumulates their dot product with quantized activations. The kernel can use the weight matrix without writing a dense copy to GPU memory. [Qwen model file](https://huggingface.co/EchoLabs33/qwen2.5-coder-3b-hxq/blob/main/qwen2.5-coder-3b-instruct-hxq-affine6.gguf) · [CUDA dispatch](https://github.com/echo313unfolding/llama.cpp/blob/580e9a213056f4168d4b54945055f98e72d53c44/ggml/src/ggml-cuda/mmvq.cu) · [Dot-product kernel](https://github.com/echo313unfolding/llama.cpp/blob/580e9a213056f4168d4b54945055f98e72d53c44/ggml/src/ggml-cuda/vecdotq.cuh#L757-L797)

These two runs measured 128-token generation. Q4_K_M was fastest in both.

| GPU | Model | AF6 (tok/s) | Q6_K (tok/s) | Q4_K_M (tok/s) | Source |
| --- | --- | ---: | ---: | ---: | --- |
| Quadro T2000 | Zamba2-2.7B-Instruct | 16.27 | 15.73 | 18.73 | [October benchmark](https://github.com/echo313unfolding/helix-substrate/blob/2a951fd3044a6d142e62f8e3015cd8a49a301c4c/receipts/hxq_af6_probe_qwen3b/MODEL_ZOO_REGRESSION_MATRIX_v3.md#L384-L395) |
| RTX 3090 | Qwen2.5-Coder-3B-Instruct | 226.53 | 204.86 | 245.03 | [Model card](https://huggingface.co/EchoLabs33/qwen2.5-coder-3b-hxq#gguf-runtime-benchmark-rtx-3090) |

## What survives compression

**Retrieval.** HelixLinear reduced the 37 linear layers in all-MiniLM-L6-v2 from 43.1 MB to 10.9 MB. Across eight queries over 46 project documents, the compressed encoder returned the same first result as the dense encoder seven times. Building the index took 0.339 seconds with the dense encoder and 1.531 seconds with the compressed one. [Per-query results](https://github.com/echo313unfolding/helix-substrate/blob/2a951fd3044a6d142e62f8e3015cd8a49a301c4c/receipts/compressed_rag_demo/rag_demo_20260312T122004.json)

**Embedding storage.** I tested AF6 on 1,024 MS MARCO passage embeddings with 768 values each. At 6.25 bits per value, the lowest cosine similarity between an original embedding and its reconstruction was 0.999558. [Per-embedding results](https://github.com/echo313unfolding/helix-codec/blob/874ab56a6a71fe95a74bbb225d7b67d4658acf22/receipts/hxq_non_llm_embedding_proof.json)

**Attention memory.** On three prompts, the first compressed key/value cache pipeline averaged 0.649446 cosine similarity to the reference attention outputs across 264 entries, below its 0.98 target. The follow-up kept layer 0 exact. Across 252 entries from layers 1–21 on the same prompts, the mean was 0.99972873. [Original run](https://github.com/echo313unfolding/helix-online-kv/blob/b8889118f0d8dfaa09365f86ec0396f5b9daaaaa/receipts/cdc01_compressed_attention_long_20260325T230153.json) · [Follow-up](https://github.com/echo313unfolding/helix-online-kv/blob/b8889118f0d8dfaa09365f86ec0396f5b9daaaaa/receipts/cdc03_hybrid_layers1_21_20260326T100511.json)

## Security triage with MorphSAT

MorphSAT tests when an agent has enough evidence to issue a verdict. On 20 fixed security-triage scenarios, Qwen2.5-Coder-7B returned the expected verdict in 14 cases with neutral prompts. When the monitor supplied its recommendation and the model made the final call, all 20 verdicts matched. [Monitor code](https://github.com/echo313unfolding/MorphSAT/blob/14689b7a36f263c81a1a9f41522d55546fd76992/morphsat/shadow_monitor.py) · [Per-scenario verdicts](https://github.com/echo313unfolding/MorphSAT/blob/14689b7a36f263c81a1a9f41522d55546fd76992/receipts/morphsat_v83_early_verdict_guard/morphsat_v83_gate_authority_20260508T125045Z.json)

Echo Labs LLC · [Published models](https://huggingface.co/EchoLabs33)
