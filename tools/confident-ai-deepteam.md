# Confident AI (DeepTeam)

**Tier:** A  
**Type:** Red Teaming + Eval  
**License:** Apache 2.0 (OSS)  
**Stars/Adoption:** CI/CD teams (Enterprise)  
**Region:** Global

## Overview

Confident AI (DeepTeam) is an open-source red teaming and evaluation framework for LLM applications. It provides 50+ vulnerability tests, OWASP and NIST mapping, and pytest integration for CI-native security testing. It is designed to be run in CI/CD pipelines, making it a developer-first security tool rather than a runtime guardrail.

## Setup experience

### Installation

```bash
pip install deep-team
```

Install time: ~15 seconds.  
No API key required — fully open-source.

### Configuration

```python
from deep_team import DeepTeam

team = DeepTeam(
    model="gpt-4o",  # or any OpenAI-compatible endpoint
    vulnerabilities=[
        "prompt_injection",
        "jailbreak",
        "data_exfiltration",
        "hallucination",
        "toxicity",
        "pii_leakage",
        "copyright_infringement",
        "bias"
    ],
    standards=["owasp_llm_top_10", "nist_ai_rmf"]
)

# Run red team evaluation
results = team.red_team(
    target="your-llm-application-endpoint",
    num_tests=100
)

# CI-native: pytest integration
# test_security.py
from deep_team import DeepTeam

def test_prompt_injection_resistance():
    team = DeepTeam(vulnerabilities=["prompt_injection"])
    results = team.red_team(target=target, num_tests=50)
    assert results.pass_rate > 0.95, f"Prompt injection pass rate: {results.pass_rate}"
```

**Ops burden:** Low. Pre-configured vulnerability tests and standards mapping. pytest integration makes it CI-native. Custom vulnerability tests can be defined via Python plugins.

### First blocked adversarial prompt

DeepTeam is a **red teaming tool**, not a runtime guardrail. It does not block prompts in production — it evaluates whether your application *would* block them. The first vulnerability test runs in ~2 minutes from `pip install`.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | 50+ vulnerability tests; OWASP and NIST mapped; comprehensive coverage |
| Latency | N/A | Red teaming tool; not a runtime guardrail; evaluated offline or in CI |
| Token economics | Very low | Self-hosted; token costs only for the LLM being tested |
| Scale behavior | Good | CI-native; parallel test execution; pytest integration |
| Ops burden | Low | Pre-configured tests; pytest integration; CI-native |
| Developer experience | Excellent | Python-native; pytest integration; good docs; active community |
| Data sovereignty | Excellent | Fully self-hosted; no external calls (except to the LLM being tested) |

## Known bypasses

### Red team tool limitation
DeepTeam is a **red teaming tool**, not a runtime guardrail. It evaluates vulnerabilities but does not prevent them in production. An attacker can exploit a vulnerability that DeepTeam identified but that the application did not fix. **Workaround:** Run DeepTeam in CI/CD before every deployment; fix vulnerabilities before they reach production.

### Novel attack blind spot
DeepTeam's vulnerability tests are based on known attack patterns. Novel attacks that do not match any of the 50+ tests will not be detected. **Workaround:** Regularly update DeepTeam to get new vulnerability tests; supplement with custom red team exercises.

### Model-specific bypass
DeepTeam tests the target LLM application. If the application uses a different model in production than in testing (e.g., testing on GPT-4o but deploying on GPT-3.5), vulnerabilities may be different. **Workaround:** Test on the exact model and configuration used in production.

### CI/CD timing bypass
If DeepTeam runs in CI/CD but the deployment happens before the test completes, vulnerabilities may reach production. **Workaround:** Block deployment until DeepTeam tests pass; or run tests on the staging environment before production deployment.

## False positive examples

DeepTeam does not have false positives in the traditional sense — it is a red teaming tool, not a runtime guardrail. However, it can report false vulnerabilities (false positives in the evaluation) if the test is overly aggressive or the target application is misconfigured in the test environment.

| Benign test case | Why it was flagged | How to tune |
|-----------------|-------------------|-------------|
| "Explain how SQL injection works for a security training" | "sql_injection" vulnerability test flags this as a potential attack | Add `security_training` context to the test configuration or use a more nuanced test |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | OWASP and NIST mapping; comprehensive vulnerability coverage |
| EU AI Act | Strong | Vulnerability tests support Article 15 (conformity with design); CI-native testing supports continuous compliance |
| SOC 2 | Moderate | CI-native testing supports audit trails; no built-in SOC 2 controls |
| ISO 27001 | Moderate | Vulnerability testing supports ISMS; no built-in ISO 27001 controls |

**Strength:** DeepTeam is the only Tier A tool that is explicitly CI-native. Its pytest integration makes it uniquely suited for developer-first security workflows.

## Comparison with peers

| vs. | Confident AI (DeepTeam) wins | Confident AI (DeepTeam) loses |
|-----|-----------------------------|-------------------------------|
| PyRIT (Microsoft) | CI-native; pytest integration; OWASP/NIST mapping | PyRIT has more advanced multi-turn orchestration and custom campaign support |
| Garak (NVIDIA) | CI-native; pytest integration; standards mapping | Garak has 100+ probes and broader model provider support |
| Lakera Guard | Free; open-source; CI-native; developer-first | Lakera is a runtime guardrail; DeepTeam is evaluation-only |

## When to use

- You want **CI-native security testing** for LLM applications
- You need **OWASP LLM Top 10 and NIST AI RMF coverage** in your testing pipeline
- You want **pytest integration** for developer-first security workflows
- You need **50+ vulnerability tests** with comprehensive coverage
- You want a **free, open-source** red teaming tool

## When to avoid

- You need a **runtime guardrail** (DeepTeam is evaluation-only, not production blocking)
- You need **real-time prompt injection defense** (use Lakera Guard, Guardrails AI, etc.)
- You need **sub-50ms latency** (DeepTeam is offline/CI evaluation, not runtime)
- You need **enterprise compliance automation** (DeepTeam is testing-focused, not compliance management)

## Version notes

- **Tested version:** DeepTeam 0.5.x (latest stable as of 2026-06)
- **Last update:** Active; weekly vulnerability test updates
- **Community:** Open-source; Apache 2.0; growing community; active GitHub

## License

Content: CC BY 4.0  
Tool: Apache 2.0 (Open Source)
