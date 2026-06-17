# Arthur AI Shield

**Tier:** A  
**Type:** LLM Firewall  
**License:** Proprietary  
**Stars/Adoption:** Enterprise  
**Region:** US

## Overview

Arthur AI Shield is a real-time LLM firewall that detects prompt injection, jailbreaks, and hallucination risks at the API layer. It sits between the application and the LLM provider, inspecting both prompts and responses. Arthur AI is also known for its model evaluation and monitoring platform (Arthur Bench), making Shield part of a broader AI governance stack.

## Setup experience

### Installation

Arthur AI Shield is a **cloud-native service** with an inline proxy or SDK integration.

```bash
pip install arthur-ai-shield
```

Install time: ~10 seconds.  
API key setup: Enterprise onboarding via Arthur AI portal.

### Configuration

```python
from arthur_ai import Shield

shield = Shield(
    api_key=os.environ["ARTHUR_AI_API_KEY"],
    org_id="your-org-id",
    policies=[
        "prompt_injection",
        "jailbreak",
        "hallucination_scoring",
        "data_exfiltration",
        "topic_restriction"
    ]
)

result = shield.inspect(
    prompt=user_input,
    response=response_text,  # optional: also inspects responses
    model_id="gpt-4o"
)

if result.risk_score > 0.75:
    block_request(result.reason)
```

**Ops burden:** Low. Policies are pre-tuned for production; minimal configuration required. The unique feature is **hallucination scoring** — Shield evaluates the confidence of the LLM response itself, not just the input. This requires response-side integration.

### First blocked adversarial prompt

With default policies, prompt injection and jailbreaks are blocked. Time to first block: ~1 minute from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Very High | Dual-side inspection (input + response); hallucination scoring is unique |
| Latency | Low | ~20-40ms inline proxy overhead |
| Token economics | Moderate | Enterprise pricing; volume-based |
| Scale behavior | Excellent | Cloud-native; auto-scaling |
| Ops burden | Very Low | Pre-tuned policies; minimal configuration |
| Developer experience | Good | Simple SDK; Arthur AI ecosystem integration |
| Data sovereignty | Moderate | Cloud-native; enterprise data residency options |

## Known bypasses

### Hallucination scoring blind spot
Arthur's hallucination scoring uses the LLM's own confidence signals (token probabilities, etc.). If the LLM is overconfident on a wrong answer, the score may not flag it. **Workaround:** Use Shield as one layer of a defense-in-depth strategy; do not rely solely on hallucination scores for high-stakes decisions.

### Response-side timing attack
If Shield inspects the response after it has been generated, the LLM provider has already processed the prompt. An attacker can craft a prompt that causes the LLM to leak data in the response, and Shield may detect it but the leak has already occurred. **Workaround:** Use the prompt-only inspection mode for high-risk applications; or architect the system so the LLM provider does not have outbound network access.

### Multi-turn hallucination cascade
In multi-turn conversations, the LLM may hallucinate context from earlier turns. Shield's per-request hallucination scoring may not catch errors that accumulate across turns. **Workaround:** Implement conversation-level hallucination audits or use a conversation-aware guardrail.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Summarize this earnings report: [financial data]" | Hallucination scorer flags low-confidence summaries of complex financial data | Lower threshold for finance domain or use topic-specific policies |
| "Translate this medical report to Spanish" | Medical terminology triggers topic restriction | Add `medical_translation` as an allowed topic |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Arthur AI's governance platform (Arthur Bench) supports full NIST lifecycle |
| EU AI Act | Strong | GDPR-compliant processing; EU data residency available |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** Arthur AI's broader platform (Arthur Bench, Arthur Shield) provides end-to-end AI governance, making compliance mapping more comprehensive than single-purpose tools.

## Comparison with peers

| vs. | Arthur AI Shield wins | Arthur AI Shield loses |
|-----|----------------------|------------------------|
| Lakera Guard | Hallucination scoring; response-side inspection; broader governance platform | Slightly higher latency; more complex setup for response-side integration |
| Prompt Security | Hallucination scoring; independent (not tied to endpoint security platform) | No XDR/endpoint correlation; narrower threat intelligence |
| Fiddler AI | Real-time firewall (Fiddler is observability-first) | Fiddler has deeper observability and ML drift detection |

## When to use

- You need **hallucination scoring** in addition to prompt injection defense
- You want **response-side inspection** (detecting data exfiltration in LLM outputs)
- You are already using **Arthur AI Bench** for model evaluation and want unified governance
- You need **enterprise compliance** (SOC 2, ISO 27001) with minimal setup

## When to avoid

- You need **full data sovereignty** without cloud dependency
- You are not using Arthur AI's broader platform and want a standalone firewall
- You need **sub-10ms latency** (response-side inspection adds overhead)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Cloud service (2026-06)
- **Last update:** Active; quarterly releases
- **Enterprise:** Available via Arthur AI sales; no self-hosted option

## License

Content: CC BY 4.0  
Tool: Proprietary (Arthur AI)
