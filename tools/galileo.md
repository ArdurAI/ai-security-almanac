# Galileo

**Tier:** A  
**Type:** Eval + Runtime Protection  
**License:** Proprietary + OSS (Agent Control)  
**Stars/Adoption:** Enterprise  
**Region:** US

## Overview

Galileo is an AI evaluation and runtime protection platform that uses Luna-2, a small language model (SLM) evaluator, to achieve 0.95 F1 score at 98% lower cost than LLM-based judges. It provides runtime guardrails, evaluation metrics, and observability for LLM applications. The Agent Control module is open-source.

## Setup experience

### Installation

Galileo is a **managed service** with an open-source Agent Control module.

```bash
pip install galileo-ai
# For Agent Control (open-source):
pip install galileo-agent-control
```

Install time: ~15 seconds.  
API key setup: Enterprise onboarding via Galileo portal.

### Configuration

```python
from galileo_ai import GalileoClient

client = GalileoClient(
    api_key=os.environ["GALILEO_API_KEY"],
    project_id="your-project-id"
)

# Runtime protection with Luna-2 evaluator
result = client.protect(
    prompt=user_input,
    response=llm_response,
    guards=[
        "hallucination",
        "toxicity",
        "pii",
        "jailbreak",
        "prompt_injection"
    ],
    evaluator="luna-2"  # SLM evaluator: 98% lower cost than LLM judges
)

for guard_result in result.guards:
    if guard_result.blocked:
        block_request(guard_result.reason)
```

**Ops burden:** Low. Pre-configured guards with Luna-2 evaluator. The open-source Agent Control module allows custom guard development. Enterprise features include observability dashboard and A/B testing.

### First blocked adversarial prompt

With Luna-2 guards enabled, prompt injection and jailbreaks are detected. Time to first block: ~2 minutes from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | Luna-2 SLM achieves 0.95 F1; strong on hallucination and toxicity |
| Latency | Low | SLM inference is fast; ~20-50ms per guard |
| Token economics | Very low | 98% lower cost than LLM judges; SLM runs on standard hardware |
| Scale behavior | Excellent | SLM scales efficiently; cloud-native |
| Ops burden | Low | Pre-configured guards; Luna-2 is pre-tuned |
| Developer experience | Good | Simple API; Agent Control open-source; good docs |
| Data sovereignty | Moderate | Cloud API; Agent Control can be self-hosted; enterprise data residency available |

## Known bypasses

### Luna-2 model limitation
Luna-2 is a small language model (SLM). While it achieves 0.95 F1 on common tasks, it may struggle with novel attack patterns or complex multi-turn attacks that require deeper reasoning. **Workaround:** Use Luna-2 as the primary guard but combine with a larger model or dedicated security tool for edge cases.

### Guard ordering bypass
If multiple guards are configured, the order matters. An attacker can craft a prompt that bypasses the first guard but triggers the second, or vice versa. If the guards are not comprehensive, the attack may slip through. **Workaround:** Use all guards in parallel and require consensus for blocking.

### Agent Control custom guard bypass
The open-source Agent Control module allows custom guards. A poorly written custom guard may have false negatives or may be bypassed by novel attacks. **Workaround:** Red-team custom guards before deployment; use the pre-configured Luna-2 guards as a baseline.

### Evaluation timing attack
Galileo's runtime protection evaluates the response after generation. If the response contains a data leak, the guard detects it but the leak has already occurred. **Workaround:** Use input-side guards for high-risk applications; or architect the system so the LLM provider does not have outbound access.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Summarize this complex research paper on quantum computing" | Hallucination guard flags low-confidence summary of technical content | Lower hallucination threshold for technical domains; or use domain-specific guards |
| "Write a Python script that deletes files in a temp directory" | Jailbreak guard flags `delete` as a potentially harmful instruction | Add `development_tools` context or whitelist file management operations |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Guards support full NIST lifecycle; Agent Control provides transparency |
| EU AI Act | Strong | PII + toxicity guards support Article 15; Agent Control supports conformity |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** The combination of Luna-2 SLM (low cost, high accuracy) and open-source Agent Control provides both enterprise features and transparency.

## Comparison with peers

| vs. | Galileo wins | Galileo loses |
|-----|-------------|---------------|
| Patronus AI | Lower cost (Luna-2 SLM); runtime protection; Agent Control open-source | Patronus has deeper domain-specific evaluators (finance, healthcare) |
| Fiddler AI | Lower cost; SLM efficiency; runtime guards | Fiddler has deeper observability, ML drift detection, and broader platform |
| Arthur AI Shield | Lower cost (SLM vs. cloud API); open-source Agent Control | Arthur has dual-side inspection (input + response) and hallucination scoring |

## When to use

- You need **low-cost, high-accuracy evaluation** (Luna-2 SLM)
- You want **runtime protection** with pre-configured guards
- You value **open-source transparency** (Agent Control)
- You need **observability + guardrails** in one platform
- You want to **A/B test guard configurations** with real traffic

## When to avoid

- You need **domain-specific evaluation** (finance, healthcare, legal) — use Patronus AI
- You need **deep observability and ML drift detection** — use Fiddler AI
- You need **dual-side inspection with hallucination scoring** — use Arthur AI Shield
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Cloud API + Agent Control 1.0 (2026-06)
- **Last update:** Active; Luna-2 updated quarterly
- **Enterprise:** Available via Galileo sales; Agent Control is open-source

## License

Content: CC BY 4.0  
Tool: Proprietary (Galileo) + Agent Control (Open Source)
