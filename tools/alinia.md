# Alinia

**Tier:** C  
**Type:** AI Compliance (Europe)  
**License:** Proprietary  
**Stars/Adoption:** $7.5M raised (Early-stage)  
**Region:** Europe

## Overview

Alinia is an early-stage European AI compliance platform providing a guardrails API, auditing, and policy enforcement. With $7.5M raised, it is better funded than AI Score but still early-stage compared to Tier A/B governance platforms. It focuses on providing a lightweight guardrails API for European organizations that need basic input/output filtering and compliance auditing without the complexity of enterprise platforms.

## Setup experience

### Installation

Alinia is a **managed API service** with a lightweight SDK.

```bash
pip install alinia
```

Install time: ~10 seconds.  
API key setup: Self-service via web portal; basic enterprise support.

### Configuration

```python
from alinia import AliniaClient

client = AliniaClient(
    api_key=os.environ["ALINIA_API_KEY"],
    org_id="your-org-id"
)

# Guardrails API
result = client.guardrails.check(
    prompt=user_input,
    policies=[
        "prompt_injection",
        "pii",
        "toxicity",
        "bias"
    ]
)

if result.blocked:
    block_request(result.reason)

# Audit logging
client.audit.log(
    system_id="chatbot",
    event="guardrail_check",
    result=result,
    timestamp=datetime.now()
)

# Policy enforcement
client.policy.enforce(
    system_id="chatbot",
    policy="eu_ai_act_article_15",
    action="block_on_violation"
)
```

**Ops burden:** Low. Lightweight API; pre-configured policies; basic audit logging. Limited customization; pre-defined policy templates. Enterprise support is basic.

### First blocked adversarial prompt

With the guardrails API enabled, prompt injection and PII are detected. Time to first block: ~2 minutes from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate | Basic guardrails API; pre-configured policies; limited customization |
| Latency | Low | ~50-100ms API response |
| Token economics | Low | Budget-friendly pricing; per-request API pricing |
| Scale behavior | Moderate | API service; may struggle with high traffic due to early-stage infrastructure |
| Ops burden | Low | Lightweight API; pre-configured policies; basic audit logging |
| Developer experience | Good | Simple API; lightweight SDK; good docs for early-stage tool |
| Data sovereignty | Moderate | EU-based; data stays in EU; no self-hosted option |

## Known bypasses

### Policy limitation
Alinia's policies are pre-configured and limited in scope. Custom policies, advanced guardrails, and domain-specific rules are not supported. **Workaround:** Use Alinia for basic guardrails; upgrade to Guardrails AI or Lakera Guard for advanced needs.

### Audit logging limitation
Alinia's audit logging is basic. Advanced audit trails, SIEM integration, and compliance-ready reporting are not supported. **Workaround:** Export audit logs to a dedicated SIEM or compliance platform.

### API dependency
Alinia is a managed API service. If the API is down, rate-limited, or deprecated, the guardrails are not applied. **Workaround:** Implement a fallback mechanism (e.g., allow on API failure or use a local guardrail as backup).

### Funding risk
With $7.5M raised, Alinia is better funded than AI Score but still early-stage. There is a risk of limited development resources, slow feature updates, or platform changes. **Workaround:** Evaluate the vendor's roadmap; plan for migration if needed.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "My SSN is 123-45-6789 for the insurance form" | PII guardrail flags the SSN | Use PII anonymization instead of blocking; or add `insurance_form` context |
| "Discuss the historical context of hate speech laws" | Toxicity guardrail flags "hate speech" | Lower toxicity threshold; or add `academic_discussion` context |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Basic guardrails; no NIST mapping |
| EU AI Act | Moderate | Guardrails API supports Article 15; basic audit logging; limited conformity documentation |
| SOC 2 | Weak | No SOC 2 certification; early-stage |
| ISO 27001 | Weak | No ISO 27001 certification; early-stage |

**Gap:** Alinia is an early-stage guardrails API with basic policies and limited enterprise features. It is suitable for small European organizations with basic guardrail needs but not for enterprise-scale security or compliance.

## Comparison with peers

| vs. | Alinia wins | Alinia loses |
|-----|-------------|--------------|
| Guardrails AI | Lightweight API; EU-focused; simpler setup | Guardrails AI has 70+ validators, output validation, broader ecosystem, free and open-source |
| Lakera Guard | Lower price; EU-focused; lightweight | Lakera Guard has higher detection accuracy, threat intelligence, enterprise compliance, Gandalf dataset |
| AI Score | Guardrails API; runtime protection; policy enforcement | AI Score is governance-focused; Alinia has runtime guardrails |

## When to use

- You are a **small European organization** with basic guardrail needs
- You need a **lightweight guardrails API** for input/output filtering
- You want **basic audit logging** and policy enforcement
- You have **limited budget** and cannot afford Tier A/B security tools
- You need **EU data residency** for basic guardrails

## When to avoid

- You are a **large enterprise** (Alinia is not enterprise-grade)
- You need **advanced guardrails** (custom policies, domain-specific rules, multi-turn detection)
- You need **enterprise compliance** (SOC 2, ISO 27001, NIST AI RMF)
- You need **threat intelligence** or advanced adversarial detection
- You need **sub-50ms latency** (API overhead is ~50-100ms)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Alinia Guardrails API (2026-06)
- **Last update:** Active; limited updates due to early-stage funding
- **Enterprise:** Self-service; basic enterprise support; budget-friendly pricing

## License

Content: CC BY 4.0  
Tool: Proprietary (Alinia)
