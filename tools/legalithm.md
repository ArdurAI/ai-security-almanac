# Legalithm

**Tier:** B  
**Type:** EU AI Act Compliance  
**License:** Free (SME window)  
**Stars/Adoption:** EU Startups/SMEs (Growing)  
**Region:** EU

## Overview

Legalithm is a free compliance tool for EU startups and SMEs to navigate the EU AI Act. It provides an applicability checker, risk classification, and Annex IV documentation support. It is free for EU startups and SMEs through approximately April 2028, making it the most accessible compliance tool in the almanac. Its focus is narrow — EU AI Act only — but its price point is unbeatable for small organizations.

## Setup experience

### Installation

Legalithm is a **free web-based tool** with no installation required.

```bash
# No installation — web-based
# Or pip install for SDK access:
pip install legalithm
```

Setup time: ~0 minutes (web) or ~10 seconds (SDK).  
No API key required for basic features; free tier available for EU startups/SMEs.

### Configuration

```python
from legalithm import LegalithmClient

client = LegalithmClient(
    org_id="your-org-id",
    region="EU"  # Required for free SME tier
)

# EU AI Act applicability checker
applicability = client.check_applicability(
    system_type="chatbot",
    use_case="customer_service",
    data_types=["personal_data", "non_personal_data"],
    user_types=["consumers", "employees"]
)

# Risk classification
risk = client.classify_risk(
    system_id="customer-service-bot",
    factors=[
        "autonomy_level",
        "user_vulnerability",
        "data_sensitivity",
        "impact_scope"
    ]
)

# Annex IV documentation
annex_iv = client.generate_annex_iv(
    system_id="customer-service-bot",
    risk_class=risk.classification,
    include_sections=[
        "system_description",
        "intended_purpose",
        "risk_assessment",
        "mitigation_measures",
        "conformity_assessment"
    ]
)
```

**Ops burden:** Very low. Web-based tool with guided workflows. Applicability checker is a questionnaire. Risk classification is automated. Annex IV documentation is template-based. No configuration required for basic use.

### First blocked adversarial prompt

Legalithm is a **compliance tool**, not a runtime security tool. It does not block prompts in production. Instead, it helps organizations understand their EU AI Act obligations and generate compliance documentation. The first applicability check runs in ~5 minutes (questionnaire completion).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | N/A | Compliance tool; not a runtime security tool |
| Latency | N/A | Compliance tool; not a runtime security tool |
| Token economics | Excellent | Free for EU startups/SMEs through ~April 2028 |
| Scale behavior | Moderate | Web-based; limited to SME scale; enterprise tier available |
| Ops burden | Very low | Web-based; guided workflows; no configuration required |
| Developer experience | Good | Simple SDK; web-based; good docs; EU-focused |
| Data sovereignty | Good | EU-based; data stays in EU; no external calls to non-EU services |

## Known bypasses

### Compliance gap
Legalithm identifies compliance gaps but does not enforce them. An organization can run an applicability check, identify a gap, and choose not to remediate it. **Workaround:** Integrate Legalithm with runtime security tools and governance platforms to enforce compliance.

### SME limitation
Legalithm is free for EU startups and SMEs. Large enterprises must use the paid tier or a different compliance tool. The free tier has limited features compared to enterprise compliance platforms. **Workaround:** Upgrade to paid tier for enterprise features; or use Credo AI or Holistic AI for enterprise compliance.

### Regulation scope limitation
Legalithm focuses on the EU AI Act only. Organizations that need compliance with NIST AI RMF, NYC LL 144, ISO 42001, or other regulations must use additional tools. **Workaround:** Use Legalithm for EU AI Act + Holistic AI or Credo AI for other regulations.

### April 2028 deadline
Legalithm's free SME tier expires approximately April 2028. After that, SMEs must pay for the service or migrate to a different compliance tool. **Workaround:** Plan for the transition; budget for paid tier; or migrate to an open-source alternative.

## False positive examples

Legalithm does not have false positives in the traditional sense — it is a compliance tool, not a runtime security tool. However, it can generate false compliance findings (false positives in the assessment) if the questionnaire is answered incorrectly or the risk classification is overly conservative.

| Benign system | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Internal chatbot for employee questions" | Risk classification flags as "high risk" due to employee data processing | Add `internal_use_only` context or review the risk classification criteria |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | EU AI Act focused; no NIST mapping |
| EU AI Act | Excellent | Applicability checker; risk classification; Annex IV documentation; free for SMEs |
| SOC 2 | Weak | No SOC 2 support; EU-focused |
| ISO 27001 | Weak | No ISO 27001 support; EU-focused |
| ISO 42001 | Moderate | Annex IV documentation supports ISO 42001 structure; no formal mapping |
| NYC LL 144 | Weak | No NYC LL 144 support; EU-focused |

**Strength:** Legalithm is the only free compliance tool in the almanac. Its EU AI Act focus, free SME tier, and guided workflows make it the ideal starting point for EU startups and small organizations navigating AI regulation.

## Comparison with peers

| vs. | Legalithm wins | Legalithm loses |
|-----|---------------|-----------------|
| Credo AI | Free for EU SMEs; simpler; EU AI Act focused; guided workflows | Credo AI has broader governance platform (AI inventory, risk assessments, policy packs) and deeper compliance mapping |
| Holistic AI | Free for EU SMEs; simpler; EU AI Act focused; no enterprise complexity | Holistic AI has automated model cards, multiple regulations, and compliance fast-lane |
| RegulAI | Free for EU SMEs; EU AI Act focused; guided workflows | RegulAI has real-time regulation updates and 3,200+ global regulations |

## When to use

- You are an **EU startup or SME** and need free EU AI Act compliance
- You need a **simple, guided** applicability checker and risk classification
- You need **Annex IV documentation** for EU AI Act conformity
- You want a **EU-focused** compliance tool with no global complexity
- You are navigating the **EU AI Act for the first time** and need a starting point

## When to avoid

- You are a **large enterprise** (free SME tier is limited; use Credo AI or Holistic AI)
- You need **compliance with multiple regulations** (NIST, NYC LL 144, ISO 42001)
- You need a **runtime security tool** (Legalithm is compliance, not runtime blocking)
- You need **prompt injection detection** or adversarial defense (use Lakera Guard, Guardrails AI, etc.)
- You are **outside the EU** (Legalithm is EU-focused; use Credo AI or Holistic AI)
- You need **sub-50ms latency** (Legalithm is not a runtime tool)

## Version notes

- **Tested version:** Legalithm Web Platform (2026-06)
- **Last update:** Active; EU AI Act updates quarterly; free SME tier active through ~April 2028
- **Enterprise:** Free tier for EU startups/SMEs; paid tier for large enterprises

## License

Content: CC BY 4.0  
Tool: Free (SME tier) / Proprietary (Enterprise tier)
