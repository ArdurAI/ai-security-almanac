# Holistic AI

**Tier:** B  
**Type:** Compliance Fast-lane  
**License:** Proprietary  
**Stars/Adoption:** Enterprise (Regulated)  
**Region:** US/EU

## Overview

Holistic AI provides a "compliance fast-lane" for regulated organizations, with pre-built checklists for EU AI Act, NYC LL 144, and ISO 42001. It automates model card generation, risk assessment, and compliance documentation. It is designed for organizations that need to quickly achieve compliance with multiple AI regulations without building governance infrastructure from scratch.

## Setup experience

### Installation

Holistic AI is a **managed SaaS platform** with optional on-premise deployment for enterprise customers.

```bash
pip install holistic-ai
```

Install time: ~15 seconds (SDK).  
Enterprise onboarding: Via Holistic AI sales; includes compliance consultant.

### Configuration

```python
from holistic_ai import HolisticClient

client = HolisticClient(
    api_key=os.environ["HOLISTIC_AI_API_KEY"],
    org_id="your-org-id"
)

# Compliance fast-lane
compliance = client.run_compliance_fastlane(
    systems=[
        {"name": "customer-service-bot", "type": "chatbot", "risk_level": "high"},
        {"name": "hiring-assistant", "type": "recruiting", "risk_level": "high"}
    ],
    regulations=["eu_ai_act", "nyc_ll_144", "iso_42001"]
)

# Automated model card generation
model_card = client.generate_model_card(
    model_id="customer-service-bot",
    include_sections=[
        "model_overview",
        "intended_use",
        "limitations",
        "ethical_considerations",
        "performance_metrics",
        "bias_assessment"
    ]
)

# Risk assessment
risk = client.assess_risk(
    system_id="customer-service-bot",
    dimensions=["bias", "privacy", "security", "transparency", "accountability"]
)

if risk.overall_score < 0.7:
    generate_remediation_plan(risk.findings)
```

**Ops burden:** Low-Moderate. Compliance fast-lane is pre-configured. Model card generation and risk assessment are automated. Custom regulations or custom model card sections require configuration. Compliance consultant helps with initial setup (~1 week).

### First blocked adversarial prompt

Holistic AI is a **compliance platform**, not a runtime security tool. It does not block prompts in production. Instead, it identifies compliance gaps and generates remediation plans. The first compliance check runs in ~1 week (governance setup + assessment).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | N/A | Compliance platform; not a runtime security tool |
| Latency | N/A | Compliance platform; not a runtime security tool |
| Token economics | Moderate | Enterprise pricing; compliance platform |
| Scale behavior | Good | SaaS platform; scales with organization size |
| Ops burden | Low-Moderate | Compliance fast-lane is pre-configured; model card generation is automated |
| Developer experience | Moderate | SDK available; compliance-focused; not developer-first |
| Data sovereignty | Moderate | SaaS platform; on-premise option available; enterprise data residency |

## Known bypasses

### Compliance gap
Holistic AI identifies compliance gaps but does not enforce them. An organization can run a compliance check, identify a gap, and choose not to remediate it. **Workaround:** Integrate Holistic AI with runtime security tools and governance platforms to enforce compliance.

### Model card limitation
Automated model card generation uses templates and pre-built sections. A novel AI system or use case may not fit the templates. **Workaround:** Create custom model card sections; or use manual model card generation for edge cases.

### Regulation update gap
Holistic AI's pre-built checklists are updated periodically. If a regulation changes between updates, the checklist may be outdated. **Workaround:** Monitor regulation updates; manually update checklists; or use a regulation monitoring service.

### Risk assessment false negative
Holistic AI's risk assessment uses automated metrics. A subtle risk that is not captured by the metrics may be missed. **Workaround:** Supplement automated risk assessment with manual review; or use a dedicated risk assessment tool.

## False positive examples

Holistic AI does not have false positives in the traditional sense — it is a compliance platform, not a runtime security tool. However, it can generate false compliance findings (false positives in the assessment) if the checklist is overly conservative or the system is misconfigured in the assessment.

| Benign system | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Internal recommendation engine for employees" | Risk assessment flags as "high risk" due to potential bias | Tune the risk assessment criteria or add `internal_use_only` context |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Pre-built checklists; model card generation supports Govern and Measure |
| EU AI Act | Strong | Pre-built checklists; Article 15, Article 52, Article 9 mapping; automated model cards |
| SOC 2 | Strong | SOC 2 Type II certified; audit trail support |
| ISO 27001 | Strong | ISO 27001 certified; ISMS integration |
| ISO 42001 | Strong | Pre-built checklists; AI management system integration |
| NYC LL 144 | Strong | Pre-built checklists; bias assessment; automated auditing |

**Strength:** Holistic AI's pre-built checklists for multiple regulations (EU AI Act, NYC LL 144, ISO 42001) and automated model card generation are unique in the Tier B landscape. It is the fastest path to compliance for regulated organizations.

## Comparison with peers

| vs. | Holistic AI wins | Holistic AI loses |
|-----|---------------|-------------------|
| Credo AI | Pre-built checklists for multiple regulations; automated model card generation; compliance fast-lane | Credo AI has broader governance platform (AI inventory, risk assessments, policy packs) and deeper NIST mapping |
| Legalithm | Enterprise-grade; automated model cards; multiple regulations; compliance fast-lane | Legalithm is free for EU SMEs; simpler; focused on EU AI Act |
| RegulAI | Automated model card generation; compliance fast-lane; pre-built checklists | RegulAI has real-time regulation updates and 3,200+ global regulations |

## When to use

- You need **quick compliance** with EU AI Act, NYC LL 144, or ISO 42001
- You want **automated model card generation**
- You need **pre-built compliance checklists** for multiple regulations
- You are a **regulated organization** with limited governance infrastructure
- You need **bias assessment** and automated risk scoring

## When to avoid

- You need a **runtime security tool** (Holistic AI is compliance, not runtime blocking)
- You need **prompt injection detection** or adversarial defense (use Lakera Guard, Guardrails AI, etc.)
- You need a **free, open-source solution** (Holistic AI is enterprise-priced)
- You need **sub-50ms latency** (Holistic AI is not a runtime tool)
- You need **developer-first security** (Holistic AI is compliance-focused, not developer-first)

## Version notes

- **Tested version:** Holistic AI Platform (2026-06)
- **Last update:** Active; regulation checklists updated quarterly; model card generation updated monthly
- **Enterprise:** Available via Holistic AI sales; on-premise option available; compliance consultant included

## License

Content: CC BY 4.0  
Tool: Proprietary (Holistic AI)
