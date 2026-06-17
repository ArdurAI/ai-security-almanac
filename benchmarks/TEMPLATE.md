# Benchmark Results — Security Suite

This is a template for publishing benchmark results. Copy this file for each benchmark run and fill in the actual results.

## Metadata

| Field | Value |
|-------|-------|
| Benchmark suite | `security-<suite>-<date>` |
| Run date | `YYYY-MM-DD` |
| Harness commit | `<git commit hash>` |
| Judge model | `<model name and version>` |
| Attack prompt library SHA-256 | `<hash>` |
| Benign prompt library SHA-256 | `<hash>` |
| Random seed | `<integer>` |
| Hardware | `<CPU/GPU spec>` |

## Tools tested

| Tool | Version | Adapter commit | Tier |
|------|---------|----------------|------|
| Guardrails AI | `x.y.z` | `<hash>` | A |
| NVIDIA NeMo Guardrails | `x.y.z` | `<hash>` | A |
| Lakera Guard | `cloud` | `<hash>` | A |
| ... | ... | ... | ... |

## Standard benchmark results

### OWASP LLM Top 10

| Tool | LLM01 Injection | LLM02 Output | LLM03 Poisoning | LLM04 DoS | LLM05 SSRF | LLM06 Access | LLM07 RCE | LLM08 Exposure | LLM09 Dependency | LLM10 Consumption | Overall |
|------|-----------------|--------------|-------------------|-----------|------------|--------------|-----------|----------------|------------------|-------------------|---------|
| Tool A | `X%` | `X%` | `N/A` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` |
| Tool B | `X%` | `X%` | `N/A` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` |

### Gandalf

| Tool | Level 1 | Level 2 | Level 3 | Level 4 | Level 5 | Level 6 | Level 7 | Overall |
|------|---------|---------|---------|---------|---------|---------|---------|---------|
| Tool A | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` |
| Tool B | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` |

## Published vs. reproduced

| Tool | Published Claim | Our Result | Delta | Verdict |
|------|----------------|------------|-------|---------|
| Tool A | "99% block rate on OWASP LLM01" | `X%` on LLM01 | `±X%` | `⚠️ / ✅` |
| Tool B | "Zero false positives" | `X%` FPR on benign set | `+X%` | `⚠️ / ✅` |

## Custom adversarial suite results

### Benign prompt set (false positive test)

| Tool | FPR (default) | FPR @ 1% target | Worst false positive | Category |
|------|--------------|-----------------|---------------------|----------|
| Tool A | `X%` | `X%` | `"<prompt>"` | `<category>` |
| Tool B | `X%` | `X%` | `"<prompt>"` | `<category>` |

### Adversarial prompt set (block rate)

| Tool | Direct | Indirect | Jailbreak | Encoding | Semantic | Multi-turn | Overall |
|------|--------|----------|-----------|----------|----------|------------|---------|
| Tool A | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` |
| Tool B | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` | `X%` |

## Stress suite results

| Tool | Encoding flood | Benign storm | Adversarial burst | Multi-turn storm | Policy chaos | Cost-runaway |
|------|---------------|--------------|-------------------|------------------|--------------|--------------|
| Tool A | `pass/fail` | `pass/fail` | `pass/fail` | `pass/fail` | `pass/fail` | `pass/fail` |
| Tool B | `pass/fail` | `pass/fail` | `pass/fail` | `pass/fail` | `pass/fail` | `pass/fail` |

## Seven-dimension scores

| Tool | Accuracy | Latency | Token Econ | Scale | Ops Burden | DevEx | Data Sov | Composite |
|------|----------|---------|------------|-------|------------|-------|----------|-----------|
| Tool A | `X` | `X` | `X` | `X` | `X` | `X` | `X` | `X` |
| Tool B | `X` | `X` | `X` | `X` | `X` | `X` | `X` | `X` |

## Confidence intervals

All scores are reported with 95% confidence intervals computed from the standard error across runs. If confidence intervals overlap between two tools, the difference is not statistically significant.

## Raw data

- `security-<suite>-<date>.json`: Full per-prompt results
- `security-<suite>-<date>-benign.json`: Benign prompt set results
- `security-<suite>-<date>-adversarial.json`: Adversarial prompt set results
- `security-<suite>-<date>-telemetry.json`: Latency, token, cost, CPU, memory telemetry

## License

Content: CC BY 4.0  
Raw data: CC BY 4.0
