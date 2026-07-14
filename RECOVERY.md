# RECOVERY.md — clean-machine reconstruction (TEMPLATE — pending home-box audit)

> **Goal:** if the home box is lost, a clean Linux machine reconstructs the legally
> publishable system + its research lineage from **public repos and artifact stores
> alone** — no chat history, no local files. Sections marked **(PENDING)** are filled by
> the home-box reconstruction audit; this file is currently a structural template only.

**manifest_status:** `provisional_public_reconstruction` — this procedure does **not** yet
pass the clean-machine gate.

## The clean-machine gate (success condition)
Start from a machine with none of the existing files, then:

1. **Bootstrap** — (PENDING) one command from the public index repo.
2. **Clone the repository set** — (PENDING) authoritative `owner/repo @ full-SHA` list, from `SYSTEM_STATUS.yaml` (respect per-repo default branch: several are `master`).
3. **Install pinned dependencies** — (PENDING) per-repo environment specs.
4. **Download public artifacts** — (PENDING) from `ARTIFACT_INDEX.yaml` (HF / GitHub Releases / mirror).
5. **Verify hashes** — (PENDING) sha256 per artifact from `ARTIFACT_INDEX.yaml`.
6. **Run component tests** — (PENDING) per-repo `verification.command` from `SYSTEM_STATUS.yaml`; promote `reported` → `verified`.
7. **Run one strict, no-fallback vertical slice** — (PENDING) the tensor → runtime → graph path (e.g. field-demo `--real-only` once it exists); fail closed on any missing dependency.
8. **Reproduce the expected final receipt-chain hash** — (PENDING) expected hash + comparison command.

## Blockers to recovery (PENDING)
Everything currently preventing the clean-machine test — from the home-box audit. Known
architectural gap today: no strict end-to-end Layer 1–6 composition exists yet
(`SYSTEM_STATUS.yaml → genuine_build_gaps`).

## Companion records
- `SYSTEM_STATUS.yaml` — current software + evidence state (provisional).
- `PUBLICATION_LEDGER.yaml` — what to push / sanitize / archive / exclude.
- `ARTIFACT_INDEX.yaml` — where every irreplaceable artifact lives publicly.
- `INTEGRATION_MATRIX.yaml` — actual production wiring per edge.
