# AI Score

**Tier:** C  
**Type:** AI Governance (Europe)  
**License:** Proprietary  
**Stars/Adoption:** $1M raised (Early-stage)  
**Region:** Europe

## Overview

AI Score is an early-stage AI governance platform focused on enterprise visibility, compliance monitoring, and risk scoring for European organizations. With only $1M in funding, it is the smallest-capitalized tool in the almanac. It provides basic AI inventory, risk scoring, and compliance monitoring but lacks the depth and maturity of Tier A/B governance platforms like Credo AI or Holistic AI.

## Setup experience

### Installation

AI Score is a **managed SaaS platform** with no self-hosted option.

```bash
pip install ai-score
```

Install time: ~10 seconds (SDK).  
Onboarding: Self-service via web portal; limited enterprise support.

### Configuration

```python
from ai_score import AIScoreClient

client = AIScoreClient(
    api_key=os.environ["AI_SCORE_API_KEY"],
    org_id="your-org-id"
)

# Basic AI inventory
inventory = client.create_inventory(
    systems=[
        {"name": "chatbot", "type": "chatbot", "risk_level": "medium"}
    ]
)

# Risk scoring
risk = client.score_risk(
    system_id="chatbot",
    dimensions=["privacy", "security", "bias"]
)

# Compliance monitoring
compliance = client.monitor_compliance(
    system_id="chatbot",
    regulations=["eu_ai_act"]
)
```

**Ops burden:** Low. Simple web-based UI; basic SDK. Limited customization; pre-defined risk dimensions. Enterprise support is minimal due to early-stage funding.

### First blocked adversarial prompt

AI Score is a **governance platform**, not a runtime security tool. It does not block prompts. It provides risk scoring and compliance monitoring. First risk score: ~5 minutes from sign-up.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | N/A | Governance platform; not a runtime security tool |
| Latency | N/A | Governance platform; not a runtime security tool |
| Token economics | Low | Budget-friendly pricing for early-stage companies |
| Scale behavior | Moderate | SaaS platform; may struggle with enterprise scale due to limited funding |
| Ops burden | Low | Simple UI; minimal configuration; limited features |
| Developer experience | Moderate | Basic SDK; limited docs; minimal community |
| Data sovereignty | Moderate | EU-based; data stays in EU; no self-hosted option |

## Known bypasses

### Feature limitation
AI Score has limited features compared to Tier A/B governance platforms. Complex risk assessments, custom regulations, and advanced compliance monitoring are not supported. **Workaround:** Use AI Score for basic governance; upgrade to Credo AI or Holistic AI for advanced needs.

### Funding risk
With only $1M raised, AI Score may not have the resources to maintain the platform, update regulations, or provide enterprise support. There is a risk of the platform being abandoned or acquired. **Workaround:** Evaluate the vendor's financial health; plan for migration to a more established platform.

### Compliance gap
AI Score identifies compliance gaps but does not enforce them. An organization can identify a gap and choose not to remediate it. **Workaround:** Integrate AI Score with runtime security tools and governance platforms to enforce compliance.

### Regulation scope limitation
AI Score focuses on the EU AI Act and basic risk dimensions. Organizations that need compliance with NIST AI RMF, NYC LL 144, ISO 42001, or other regulations must use additional tools. **Workaround:** Use AI Score for basic EU AI Act compliance + Credo AI or Holistic AI for other regulations.

## False positive examples

| Benign system | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Internal FAQ chatbot" | Risk scoring flags as "medium risk" due to basic privacy concerns | Review risk scoring criteria; adjust risk level manually |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Basic risk scoring; no NIST mapping |
| EU AI Act | Moderate | Basic applicability checker; risk scoring; limited Annex IV support |
| SOC 2 | Weak | No SOC 2 support; early-stage |
| ISO 27001 | Weak | No ISO 27001 support; early-stage |

**Gap:** AI Score is an early-stage governance platform with limited features and funding. It is suitable for small European organizations with basic governance needs but not for enterprise-scale compliance.

## Comparison with peers

| vs. | AI Score wins | AI Score loses |
|-----|---------------|----------------|
| Credo AI | Lower price; simpler; EU-focused | Credo AI has comprehensive governance platform, ISO 42001, enterprise support |
| Holistic AI | Lower price; simpler; EU-focused | Holistic AI has automated model cards, multiple regulations, compliance fast-lane |
| Legalithm | Lower price; risk scoring | Legalithm is free for EU SMEs; has applicability checker and Annex IV documentation |

## When to use

- You are a **small European organization** with basic AI governance needs
- You need **budget-friendly** risk scoring and compliance monitoring
- You want a **simple, EU-focused** governance platform
- You are navigating the **EU AI Act for the first time** and need a starting point
- You have **limited budget** and cannot afford Tier A/B governance platforms

## When to avoid

- You are a **large enterprise** (AI Score is not enterprise-grade)
- You need **comprehensive governance** (AI inventory, risk assessments, policy packs, compliance automation)
- You need **enterprise support** (limited due to early-stage funding)
- You need **compliance with multiple regulations** (NIST, NYC LL 144, ISO 42001)
- You need a **runtime security tool** (AI Score is governance, not runtime blocking)
- You need **prompt injection detection** or adversarial defense (not in scope)

## Version notes

- **Tested version:** AI Score Platform (2026-06)
- **Last update:** Active; limited updates due to funding constraints
- **Enterprise:** Self-service; limited enterprise support; budget-friendly pricing

## License

Content: CC BY 4.0  
Tool: Proprietary (AI Score)
