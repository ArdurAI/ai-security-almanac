# Garak (NVIDIA)

**Tier:** B  
**Type:** Vulnerability Scanner  
**License:** Open Source  
**Stars/Adoption:** NVIDIA (Growing)  
**Region:** US (NVIDIA)

## Overview

Garak is an open-source vulnerability scanner developed by NVIDIA that provides 100+ probes for jailbreak, data leakage, toxicity, and other LLM vulnerabilities. It supports broad model provider coverage (OpenAI, Anthropic, local models, Hugging Face, etc.) and is designed for automated vulnerability assessment and benchmarking. It is a scanner, not a runtime guardrail, and is used for pre-deployment security assessment.

## Setup experience

### Installation

```bash
pip install garak
```

Install time: ~20 seconds.  
Additional dependencies for specific model providers (e.g., `openai`, `anthropic`, `transformers`).

### Configuration

```bash
# Run a full vulnerability scan
garak --model_type openai --model_name gpt-4o --probes all

# Run specific probes
garak --model_type openai --model_name gpt-4o --probes leakreplay,dan,knownbadsignatures

# Run with custom configuration
garak --model_type huggingface --model_name meta-llama/Llama-2-7b-chat-hf \
  --probes all \
  --config garak-config.yaml
```

```python
from garak import Garak

garak = Garak(
    model_type="openai",
    model_name="gpt-4o",
    probes=["leakreplay", "dan", "knownbadsignatures", "encoding"]
)

results = garak.run()

for probe, result in results.items():
    print(f"Probe: {probe}")
    print(f"  Pass rate: {result.pass_rate}")
    print(f"  Failures: {result.failures}")
```

**Ops burden:** Low-Moderate. CLI-driven; pre-configured probes. Custom probes can be written by extending the probe base class. Broad model provider coverage requires understanding of each provider's API.

### First blocked adversarial prompt

Garak does not "block" prompts — it **scans and evaluates vulnerabilities**. The first scan runs in ~2 minutes from `pip install` (including probe selection and model configuration).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | 100+ probes; broad model provider coverage; NVIDIA backing |
| Latency | N/A | Scanner tool; not a runtime guardrail; evaluated offline or in CI |
| Token economics | Low-Moderate | Token costs for the LLM being tested; can be expensive for full scans |
| Scale behavior | Good | Parallel probe execution; supports multiple models |
| Ops burden | Low-Moderate | CLI-driven; pre-configured probes; custom probe support |
| Developer experience | Good | CLI + Python API; good docs; NVIDIA backing; active development |
| Data sovereignty | Excellent | Fully self-hosted; no external calls (except to the target LLM) |

## Known bypasses

### Scanner tool limitation
Garak is a **scanner**, not a runtime guardrail. It identifies vulnerabilities but does not prevent them in production. An attacker can exploit a vulnerability that Garak identified but that the application did not fix. **Workaround:** Run Garak regularly; fix vulnerabilities before they reach production; combine with runtime guardrails.

### Probe coverage gap
Garak's 100+ probes cover known vulnerabilities. Novel attacks that do not match any probe will not be detected. **Workaround:** Regularly update Garak to get new probes; supplement with custom probes and manual penetration testing.

### Model provider configuration bypass
If the target model configuration in Garak does not match the production configuration, the scan results may not be representative. **Workaround:** Scan on the exact production configuration; or scan on multiple configurations.

### Resource exhaustion
A full scan with 100+ probes can generate many API calls, causing rate limiting or high costs. **Workaround:** Use local models for scanning; or run probes in batches with rate limit management.

## False positive examples

Garak does not have false positives in the traditional sense — it is a scanner, not a runtime guardrail. However, it can report false vulnerabilities (false positives in the evaluation) if the probe is overly aggressive or the model is misconfigured in the scan environment.

| Benign test case | Why it was flagged | How to tune |
|-----------------|-------------------|-------------|
| "Explain how SQL injection works for a security training" | `knownbadsignatures` probe flags SQL injection keywords | Add `security_training` context to the probe configuration or use a more nuanced probe |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | Vulnerability scanning supports Measure and Govern; no built-in compliance mapping |
| EU AI Act | Moderate | Vulnerability scanning supports Article 15 (conformity with design); no built-in conformity documentation |
| SOC 2 | Partial | Vulnerability scanning supports audit trails; no built-in SOC 2 controls |
| ISO 27001 | Partial | Vulnerability scanning supports ISMS; no built-in ISO 27001 controls |

**Strength:** Garak's 100+ probes and broad model provider coverage make it the most comprehensive open-source vulnerability scanner in the almanac. NVIDIA's backing ensures ongoing development and quality.

## Comparison with peers

| vs. | Garak wins | Garak loses |
|-----|-----------|-------------|
| PyRIT (Microsoft) | 100+ probes; broader model provider coverage; CLI-driven | PyRIT has more advanced multi-turn orchestration and custom attack campaigns |
| Confident AI (DeepTeam) | 100+ probes; CLI-driven; NVIDIA backing | DeepTeam is CI-native with pytest integration and OWASP/NIST mapping |
| Lakera Guard | Free; open-source; 100+ probes; broad model coverage | Lakera Guard is a runtime guardrail with higher detection accuracy and threat intelligence |

## When to use

- You need a **free, open-source vulnerability scanner** with 100+ probes
- You want **broad model provider coverage** (OpenAI, Anthropic, local, Hugging Face, etc.)
- You need **CLI-driven scanning** for CI/CD integration
- You are a **security researcher** or developer evaluating LLM vulnerabilities
- You want **NVIDIA-backed** tooling with active development

## When to avoid

- You need a **runtime guardrail** (Garak is evaluation-only, not production blocking)
- You need **real-time prompt injection defense** (use Lakera Guard, Guardrails AI, etc.)
- You need **sub-50ms latency** (Garak is offline/CI evaluation, not runtime)
- You have **limited API budget** (full scans with 100+ probes can be expensive)
- You need **enterprise compliance automation** (Garak is scanning-focused, not compliance management)

## Version notes

- **Tested version:** Garak 0.9.x (2026-06)
- **Last update:** Active; NVIDIA team maintains; monthly probe updates
- **Community:** Open-source; growing community; NVIDIA backing

## License

Content: CC BY 4.0  
Tool: Open Source (NVIDIA)
