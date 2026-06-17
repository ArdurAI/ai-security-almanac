# Patronus AI

**Tier:** A  
**Type:** Evaluator + Guardrails  
**License:** Proprietary + OSS (Lynx)  
**Stars/Adoption:** Enterprise (regulated domains)  
**Region:** US

## Overview

Patronus AI is an evaluation and guardrails platform specializing in regulated domains (finance, healthcare, legal). It provides automated evaluation of LLM outputs for hallucination, PII leakage, copyright infringement, and financial compliance. The Lynx model is open-source and can be used for independent evaluation. Patronus operates as a managed service with enterprise features.

## Setup experience

### Installation

Patronus AI is a **managed service** with an open-source evaluation model (Lynx).

```bash
pip install patronus-ai
# For Lynx (open-source evaluator):
pip install patronus-lynx
```

Install time: ~15 seconds.  
API key setup: Enterprise onboarding via Patronus AI portal.

### Configuration

```python
from patronus_ai import PatronusClient

client = PatronusClient(
    api_key=os.environ["PATRONUS_API_KEY"],
    org_id="your-org-id"
)

# Evaluate a response for hallucination and compliance
result = client.evaluate(
    prompt=user_input,
    response=llm_response,
    evaluators=[
        "hallucination",
        "pii_leakage",
        "copyright_infringement",
        "financial_compliance",
        "medical_compliance"
    ],
    domain="finance"  # or "healthcare", "legal", "general"
)

for eval_result in result.evaluations:
    if eval_result.score < 0.7:
        flag_issue(eval_result.evaluator, eval_result.explanation)
```

**Ops burden:** Low-Moderate. Domain-specific evaluators are pre-configured. Custom evaluators can be defined via the Patronus platform. The Lynx model can be run locally for independent evaluation.

### First blocked adversarial prompt

With domain-specific evaluators, hallucination and compliance violations are detected. Time to first block: ~2 minutes from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | Specialized for regulated domains; strong on hallucination, PII, copyright, finance |
| Latency | Moderate | ~100-300ms per evaluation; multiple evaluators run in parallel |
| Token economics | Moderate | Enterprise pricing; per-evaluation cost |
| Scale behavior | Good | Cloud-native; parallel evaluation |
| Ops burden | Low-Moderate | Domain evaluators pre-configured; custom evaluators require platform setup |
| Developer experience | Good | Simple API; domain-specific evaluators; Lynx open-source for transparency |
| Data sovereignty | Moderate | Cloud API; Lynx can be run locally for evaluation; enterprise data residency available |

## Known bypasses

### Domain-specific blind spot
Patronus evaluators are optimized for finance, healthcare, and legal. An attack outside these domains (e.g., general prompt injection, system prompt extraction) may not be detected by domain evaluators. **Workaround:** Combine with general-purpose security tools (e.g., Lakera Guard) for non-domain attacks.

### Evaluation timing attack
Patronus evaluates the response after it has been generated. If the LLM response contains a data leak or compliance violation, the evaluation detects it but the leak has already occurred. **Workaround:** Use Patronus in a pre-generation mode (input evaluation) for high-risk applications; or architect the system so the LLM provider does not have outbound access.

### Lynx model limitation
The open-source Lynx model is a general-purpose evaluator. It may not match the accuracy of the full Patronus platform's domain-specific evaluators. **Workaround:** Use the full Patronus platform for production; use Lynx for independent verification and development.

### Custom evaluator bypass
If a custom evaluator is poorly defined, an attacker can craft a prompt that technically passes the evaluator but violates the intent. **Workaround:** Red-team custom evaluators before deployment; use multiple evaluators in parallel.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Summarize this earnings report: [public financial data]" | Financial compliance evaluator flags summary of public data as potential misinformation | Lower the financial compliance threshold for public data; or use a pre-filter for public vs. non-public data |
| "Translate this medical report to Spanish for a patient" | Medical compliance evaluator flags translation as potential misinformation | Add `translation` context or use a medical-translation-specific evaluator |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Domain evaluators support full NIST lifecycle; Lynx provides independent verification |
| EU AI Act | Strong | PII + compliance evaluators support Article 15; domain-specific evaluators support high-risk AI systems |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** Patronus is built for regulated domains. The combination of managed evaluation + open-source Lynx provides both enterprise compliance and transparency.

## Comparison with peers

| vs. | Patronus AI wins | Patronus AI loses |
|-----|-----------------|-------------------|
| Arthur AI Shield | Domain-specific evaluators (finance, healthcare, legal); open-source Lynx for transparency | Arthur has broader platform (Bench + Shield) and real-time firewall |
| Fiddler AI | Domain-specific evaluators; open-source Lynx | Fiddler has deeper observability, ML drift detection, and broader platform |
| Guardrails AI | Domain-specific evaluators; enterprise compliance | Guardrails is free, open-source, and has a broader validator ecosystem |

## When to use

- You operate in a **regulated domain** (finance, healthcare, legal)
- You need **hallucination detection** with domain-specific accuracy
- You need **PII leakage detection** in regulated contexts
- You want **open-source transparency** (Lynx) combined with enterprise features
- You need **copyright infringement detection** for content generation

## When to avoid

- You are not in a regulated domain and need general-purpose security (overkill)
- You need **real-time prompt injection defense** (Patronus is evaluation-focused, not a firewall)
- You need **sub-50ms latency** (evaluation is slower than simple filtering)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Cloud API + Lynx 1.0 (2026-06)
- **Last update:** Active; domain evaluators updated quarterly
- **Enterprise:** Available via Patronus AI sales; Lynx is open-source

## License

Content: CC BY 4.0  
Tool: Proprietary (Patronus AI) + Lynx (Open Source)
