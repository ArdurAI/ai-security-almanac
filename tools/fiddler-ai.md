# Fiddler AI

**Tier:** A  
**Type:** Observability + Guardrails  
**License:** Proprietary  
**Stars/Adoption:** $100M raised (Enterprise)  
**Region:** US

## Overview

Fiddler AI is an AI observability and guardrails platform that provides real-time monitoring, explainability, and safety controls for LLM applications. It is known for its "Trust Models" that run in the customer environment with <100ms latency. Fiddler combines ML monitoring (drift, performance, fairness) with LLM-specific guardrails (hallucination, safety, jailbreak detection).

## Setup experience

### Installation

Fiddler AI is a **managed service** with on-premise deployment options for enterprise customers.

```bash
pip install fiddler-ai
```

Install time: ~15 seconds.  
API key setup: Enterprise onboarding via Fiddler AI portal.

### Configuration

```python
from fiddler_ai import FiddlerClient

client = FiddlerClient(
    api_key=os.environ["FIDDLER_API_KEY"],
    url="https://your-org.fiddler.ai"
)

# Configure Trust Model (runs in customer environment)
client.configure_trust_model(
    model_id="your-llm-model",
    guards=[
        "hallucination",
        "toxicity",
        "jailbreak",
        "prompt_injection",
        "data_exfiltration"
    ],
    latency_target_ms=100  # <100ms latency target
)

# Real-time monitoring + guardrails
result = client.inspect(
    prompt=user_input,
    response=llm_response,
    model_id="your-llm-model"
)

if result.risk_score > 0.8:
    block_request(result.reason)
```

**Ops burden:** Low-Moderate. Trust Models are pre-configured and run in the customer environment. The Fiddler platform provides dashboards, alerts, and A/B testing. On-premise deployment requires infrastructure setup.

### First blocked adversarial prompt

With Trust Model guards enabled, prompt injection and jailbreaks are detected. Time to first block: ~2 minutes from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | Trust Models are fine-tuned per customer; strong on hallucination and safety |
| Latency | Very Low | <100ms Trust Models run in customer environment |
| Token economics | Moderate | Enterprise pricing; Trust Models run on customer hardware (no per-token cost) |
| Scale behavior | Excellent | On-premise Trust Models scale with customer infrastructure |
| Ops burden | Low-Moderate | Pre-configured Trust Models; dashboard-driven; on-premise setup for enterprise |
| Developer experience | Good | Simple API; comprehensive dashboards; good docs |
| Data sovereignty | Excellent | Trust Models run in customer environment; full data sovereignty |

## Known bypasses

### Trust Model drift
Trust Models are trained on a specific dataset. If the LLM application evolves (new model version, new prompt templates, new use cases), the Trust Model may drift and become less accurate. **Workaround:** Regularly retrain Trust Models with new data; use Fiddler's drift detection to trigger retraining.

### Multi-turn bypass
Trust Models evaluate per-request. A multi-turn attack that distributes the harmful content across turns may not be detected. **Workaround:** Enable conversation-level Trust Models or use a conversation-aware guardrail.

### On-premise resource exhaustion
If the Trust Model runs in the customer environment, a sustained attack can exhaust CPU/GPU resources, causing latency spikes or dropped requests. **Workaround:** Size infrastructure for attack load; use Fiddler's cloud fallback for overflow.

### Dashboard blind spot
Fiddler's dashboards are comprehensive but require human review. An attacker can craft a low-volume, high-impact attack that does not trigger automated alerts. **Workaround:** Set low thresholds for automated alerts; implement regular human review of dashboard anomalies.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Summarize this earnings report with forward-looking statements" | Hallucination guard flags forward-looking statements as potentially ungrounded | Add `financial_reporting` context or tune the grounding threshold for finance domain |
| "Write a Python script that makes HTTP requests to internal APIs" | Data exfiltration guard flags HTTP requests as potential data leakage | Add `internal_api` context or whitelist internal domains |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Trust Models support full NIST lifecycle; dashboards support Govern and Manage |
| EU AI Act | Strong | On-premise deployment = full data sovereignty; guards support Article 15 |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** Fiddler's combination of observability, explainability, and on-premise guardrails provides comprehensive AI governance. The Trust Model architecture is unique in the market.

## Comparison with peers

| vs. | Fiddler AI wins | Fiddler AI loses |
|-----|----------------|-----------------|
| Galileo | Deeper observability; ML drift detection; on-premise Trust Models | Galileo has lower-cost SLM evaluators and open-source Agent Control |
| Arthur AI Shield | Deeper observability; ML monitoring; on-premise deployment | Arthur has dual-side inspection and response-side hallucination scoring |
| Guardrails AI | Enterprise observability; on-premise deployment; ML monitoring | Guardrails is free, open-source, and has a broader validator ecosystem |

## When to use

- You need **comprehensive AI observability** (drift, performance, fairness, explainability)
- You need **on-premise guardrails** with <100ms latency
- You want **ML monitoring + LLM guardrails** in one platform
- You need **enterprise dashboards and alerts** for AI governance
- You have **GPU infrastructure** for on-premise Trust Models

## When to avoid

- You need a **free, open-source solution** (Fiddler is enterprise-priced)
- You have **no GPU infrastructure** and cannot afford on-premise deployment
- You need **prompt injection detection as the primary focus** (Fiddler is observability-first, security-second)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Cloud + On-premise Trust Models (2026-06)
- **Last update:** Active; Trust Models updated quarterly
- **Enterprise:** Available via Fiddler AI sales; on-premise option available

## License

Content: CC BY 4.0  
Tool: Proprietary (Fiddler AI)
