# Security, Guardrails & AI Safety Almanac

A living encyclopedia of AI security, guardrails, and safety tools. Updated monthly with fresh repo metadata, releases, landscape shifts, and independent benchmark results.

> Vendors publish their own benchmark numbers. Nobody reproduces them independently, and nobody evaluates tools the way a platform engineer has to live with them: ops burden, failure modes, scale curves, and cost. This almanac is the public record of that work.

## How to use this repo

| You want… | Go to |
|-----------|-------|
| The state of the landscape right now | The latest file in `editions/` |
| Everything we know about one tool | `tools/<name>.md` |
| Machine-readable roster + metadata | `data/roster.json` |
| Architecture diagrams | `architecture.md` |
| Benchmark results (rolling) | `benchmarks/` |
| How tools are tested and ranked | `methodology/benchmark-harness.md` |
| Project intent & philosophy | `INTENT.md` |
| Implementation guide & how to add tools | `IMPLEMENTATION.md` |
| Testing methodology & benchmarks | `TESTING.md` |
| Troubleshooting & debugging | `TROUBLESHOOTING.md` |
| How to contribute | `CONTRIBUTING.md` |

## The roster

**Tier A** — 19 tools: Guardrails AI, NVIDIA NeMo Guardrails, Lakera Guard (Check Point), Future AGI Protect, Prompt Security (SentinelOne), Arthur AI Shield, AWS Bedrock Guardrails, Azure AI Content Safety, Google Model Armor, OpenAI Moderation API…

**Tier B** — 43 tools: APort / OpenClaw, StackOne Defender, ClawGuard, Nova-tracer, Bifrost, Rebuff, LLM Guard (Protect AI), Noma Security, Harmonic Security, Lasso Security…

**Tier C** — 6 tools: AI Score, Alinia, Ciphero, Iridius, Capsule Security, Aigentsphere

**Total: 68 tools**

## Methodology

Results published here come from a frozen-before-results harness:
- Standard benchmarks for comparability with published claims — every ranking ships a _published vs. reproduced_ table.
- A custom PlatformOps benchmark: testing on infrastructure work — setup, reliability, scale, cost.
- A stress suite: contradiction storms, near-duplicate floods, concurrent writers, kill-the-backing-store chaos, cost-runaway measurement.
- Seven scored dimensions: accuracy, latency, token economics, scale behavior, **ops burden**, developer experience, data sovereignty.

The judge model, prompts (SHA-256-frozen), and control variables were fixed before any tool ran. Raw results JSON is published with every ranking.

## Update cadence

One edition per month under `editions/YYYY-MM.md`: refreshed metadata, notable releases, new entrants triaged in or out, and a diary of what was tested.

## License

Content is licensed CC BY 4.0 — share and adapt with attribution to **ArdurAI / Security, Guardrails & AI Safety Almanac**.
