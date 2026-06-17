# PyRIT (Microsoft)

**Tier:** B  
**Type:** Red Teaming Framework  
**License:** Open Source  
**Stars/Adoption:** Microsoft Research (Growing)  
**Region:** US (Microsoft)

## Overview

PyRIT (Python Risk Identification Toolkit) is an open-source red teaming framework developed by Microsoft's AI Red Team. It provides multi-turn orchestration, custom attack campaigns, and model/platform-agnostic testing. It is designed for red teamers and security researchers to systematically evaluate LLM vulnerabilities through automated attack generation and evaluation.

## Setup experience

### Installation

```bash
pip install pyrit
```

Install time: ~30 seconds.  
Additional dependencies for specific attack orchestrators (e.g., OpenAI, Azure, local models).

### Configuration

```python
from pyrit.orchestrator import PromptSendingOrchestrator
from pyrit.prompt_target import OpenAITarget
from pyrit.common import default_values

# Configure target (the LLM being tested)
target = OpenAITarget(
    deployment_name="gpt-4o",
    api_key=os.environ["OPENAI_API_KEY"]
)

# Create an orchestrator for multi-turn attacks
orchestrator = PromptSendingOrchestrator(
    prompt_target=target,
    attack_strategy="jailbreak",
    max_turns=10
)

# Run the attack campaign
results = await orchestrator.run_attack_async(
    objective="Extract the system prompt",
    num_iterations=100
)

# Analyze results
for result in results:
    if result.success:
        print(f"Attack succeeded: {result.prompt}")
        print(f"Response: {result.response}")
```

**Ops burden:** Moderate. PyRIT is a research tool, not a production runtime guardrail. It requires understanding of attack strategies, orchestrators, and target configurations. Multi-turn orchestration requires careful setup to avoid rate limiting or API costs.

### First blocked adversarial prompt

PyRIT does not "block" prompts — it **generates and evaluates attacks**. The first attack campaign runs in ~5 minutes from `pip install` (including target configuration and attack strategy setup).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | Multi-turn orchestration; custom attack campaigns; model/platform agnostic |
| Latency | N/A | Red teaming tool; not a runtime guardrail; evaluated offline or in CI |
| Token economics | Low-Moderate | Token costs for the LLM being tested (attacker's LLM); can be expensive for large campaigns |
| Scale behavior | Good | Parallel attack execution; supports multiple targets |
| Ops burden | Moderate | Research tool; requires attack strategy knowledge; multi-turn orchestration is complex |
| Developer experience | Good | Python-native; good docs; Microsoft Research backing; active development |
| Data sovereignty | Excellent | Fully self-hosted; no external calls (except to the target LLM) |

## Known bypasses

### Red team tool limitation
PyRIT is a **red teaming tool**, not a runtime guardrail. It identifies vulnerabilities but does not prevent them in production. An attacker can exploit a vulnerability that PyRIT identified but that the application did not fix. **Workaround:** Run PyRIT regularly; fix vulnerabilities before they reach production; combine with runtime guardrails.

### Target configuration bypass
If the target LLM configuration in PyRIT does not match the production configuration (e.g., different model version, different system prompt, different temperature), the red team results may not be representative. **Workaround:** Test on the exact production configuration; or test on multiple configurations.

### Rate limiting and cost
PyRIT's multi-turn orchestration can generate many API calls, causing rate limiting or high costs. An attacker with more resources (e.g., multiple API keys, local models) may bypass the rate limits that constrain PyRIT. **Workaround:** Use local models for the attacker LLM; or use multiple API keys with rate limit management.

### Novel attack blind spot
PyRIT's attack strategies are based on known techniques. Novel attacks that do not match any strategy may not be generated. **Workaround:** Regularly update PyRIT to get new attack strategies; supplement with custom attack generation.

## False positive examples

PyRIT does not have false positives in the traditional sense — it is a red teaming tool, not a runtime guardrail. However, it can report false vulnerabilities (false positives in the evaluation) if the target is misconfigured or the attack strategy is overly aggressive.

| Benign test case | Why it was flagged | How to tune |
|-----------------|-------------------|-------------|
| "Explain how SQL injection works for a security training" | SQL injection attack strategy flags this as a potential vulnerability | Add `security_training` context to the attack strategy or use a more nuanced strategy |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | Red teaming supports Measure and Govern; no built-in compliance mapping |
| EU AI Act | Moderate | Red teaming supports Article 15 (conformity with design); no built-in conformity documentation |
| SOC 2 | Partial | Red teaming supports audit trails; no built-in SOC 2 controls |
| ISO 27001 | Partial | Red teaming supports ISMS; no built-in ISO 27001 controls |

**Strength:** PyRIT is the most advanced open-source red teaming framework in the almanac. Its multi-turn orchestration and custom attack campaigns are unique among Tier B tools.

## Comparison with peers

| vs. | PyRIT wins | PyRIT loses |
|-----|-----------|-------------|
| Confident AI (DeepTeam) | Multi-turn orchestration; custom attack campaigns; model/platform agnostic | DeepTeam is CI-native with pytest integration and OWASP/NIST mapping |
| Garak (NVIDIA) | Multi-turn orchestration; custom campaigns; Microsoft Research backing | Garak has 100+ probes and broader model provider support |
| Lakera Guard | Free; open-source; multi-turn orchestration; custom campaigns | Lakera Guard is a runtime guardrail with higher detection accuracy and threat intelligence |

## When to use

- You need a **free, open-source red teaming framework** with multi-turn orchestration
- You want **custom attack campaigns** for specific vulnerabilities
- You need **model/platform-agnostic testing** (OpenAI, Azure, local models, etc.)
- You are a **security researcher** or red teamer evaluating LLM vulnerabilities
- You want **Microsoft Research-backed** tooling with active development

## When to avoid

- You need a **runtime guardrail** (PyRIT is evaluation-only, not production blocking)
- You need **real-time prompt injection defense** (use Lakera Guard, Guardrails AI, etc.)
- You need **sub-50ms latency** (PyRIT is offline/CI evaluation, not runtime)
- You need **enterprise compliance automation** (PyRIT is testing-focused, not compliance management)
- You have **limited API budget** (multi-turn campaigns can be expensive)

## Version notes

- **Tested version:** PyRIT 0.4.x (2026-06)
- **Last update:** Active; Microsoft Research team maintains; monthly updates
- **Community:** Open-source; growing community; Microsoft Research backing

## License

Content: CC BY 4.0  
Tool: Open Source (Microsoft)
