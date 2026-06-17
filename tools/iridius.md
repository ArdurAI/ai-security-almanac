# Iridius

**Tier:** C  
**Type:** Compliance-by-Design  
**License:** Proprietary  
**Stars/Adoption:** $8.6M raised (Early-stage)  
**Region:** Europe

## Overview

Iridius is an early-stage compliance-by-design platform for regulated European enterprises. It focuses on embedding compliance into AI workflows from the design phase rather than adding it as an afterthought. With $8.6M raised, it is better funded than most Tier C tools but still early-stage compared to Tier A/B governance platforms. It provides workflow validation, design-phase compliance checks, and regulated enterprise workflow templates.

## Setup experience

### Installation

Iridius is a **managed SaaS platform** with a workflow designer UI.

```bash
pip install iridius
```

Install time: ~10 seconds (SDK).  
Onboarding: Self-service via web portal; basic enterprise support.

### Configuration

```python
from iridius import IridiusClient

client = IridiusClient(
    api_key=os.environ["IRIDIUS_API_KEY"],
    org_id="your-org-id"
)

# Compliance-by-design workflow
workflow = client.design.workflow(
    name="customer-onboarding-chatbot",
    steps=[
        {"step": "user_input", "compliance_check": "pii_detection"},
        {"step": "llm_processing", "compliance_check": "bias_mitigation"},
        {"step": "output_validation", "compliance_check": "toxicity_filter"},
        {"step": "audit_logging", "compliance_check": "interaction_capture"}
    ],
    regulations=["eu_ai_act", "gdpr"]
)

# Workflow validation
validation = client.validate.workflow(
    workflow_id=workflow.id,
    checks=["completeness", "correctness", "regulatory_coverage"]
)

if not validation.valid:
    fix_workflow(validation.issues)

# Design-phase compliance check
compliance = client.check.compliance(
    workflow_id=workflow.id,
    regulation="eu_ai_act",
    article="article_15"
)

if not compliance.compliant:
    redesign_workflow(compliance.gaps)
```

**Ops burden:** Moderate. Workflow designer requires understanding of compliance requirements and AI workflows. Design-phase compliance checks are automated but require manual review. Enterprise support is basic.

### First blocked adversarial prompt

Iridius does not "block" prompts — it **validates workflows and designs compliance into them**. It ensures that AI workflows are compliant by design rather than detecting adversarial content at runtime. First workflow validation: ~10 minutes from sign-up (workflow design + validation).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | N/A | Compliance-by-design platform; not a runtime security tool |
| Latency | N/A | Compliance-by-design platform; not a runtime security tool |
| Token economics | Low-Moderate | Budget-friendly pricing for early-stage companies; enterprise tier available |
| Scale behavior | Moderate | SaaS platform; workflow validation is lightweight |
| Ops burden | Moderate | Workflow designer requires compliance knowledge; design-phase checks are automated but require review |
| Developer experience | Moderate | Workflow designer UI; SDK available; basic docs |
| Data sovereignty | Moderate | EU-based; data stays in EU; no self-hosted option |

## Known bypasses

### Workflow bypass
If the application does not follow the validated workflow (e.g., a developer deploys a non-compliant workflow, or the workflow is modified post-deployment), the compliance-by-design approach is ineffective. **Workaround:** Enforce workflow compliance via CI/CD gates; or use runtime guardrails as a fallback.

### Design-phase limitation
Iridius validates workflows at the design phase but does not monitor runtime behavior. A compliant workflow may still produce non-compliant outputs if the runtime model behaves unexpectedly. **Workaround:** Combine Iridius with runtime monitoring and guardrails (e.g., Guardrails AI, Lakera Guard).

### Template limitation
Iridius provides workflow templates for common regulated enterprise workflows. Custom workflows or novel AI applications may not have a template. **Workaround:** Create custom workflows from scratch; or use a more flexible governance platform.

### Funding risk
With $8.6M raised, Iridius is better funded than most Tier C tools but still early-stage. There is a risk of limited development resources, slow feature updates, or platform changes. **Workaround:** Evaluate the vendor's roadmap; plan for migration if needed.

## False positive examples

Iridius does not have false positives in the traditional sense — it is a compliance-by-design platform, not a runtime security tool. However, it can generate false compliance violations (false positives in the validation) if the workflow template is overly conservative or the compliance check is misconfigured.

| Benign workflow | Why it was flagged | How to tune |
|-----------------|-------------------|-------------|
| "Internal FAQ chatbot with minimal user data" | Workflow validation flags as "non-compliant" due to missing PII detection step | Review the workflow template; add a PII detection step or justify why it is not needed |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Design-phase compliance checks; no NIST mapping |
| EU AI Act | Moderate | Design-phase compliance checks; workflow templates; Article 15 support; limited conformity documentation |
| GDPR | Moderate | Design-phase privacy checks; GDPR workflow templates |
| SOC 2 | Weak | No SOC 2 certification; early-stage |
| ISO 27001 | Weak | No ISO 27001 certification; early-stage |

**Gap:** Iridius is an early-stage compliance-by-design platform with limited features and early-stage certification. It is suitable for regulated European enterprises that want to embed compliance into AI workflows but not for comprehensive governance or runtime security.

## Comparison with peers

| vs. | Iridius wins | Iridius loses |
|-----|-------------|---------------|
| Credo AI | Compliance-by-design approach; workflow templates; lower price | Credo AI has comprehensive governance, AI inventory, risk assessments, policy packs, enterprise compliance |
| Holistic AI | Compliance-by-design; workflow templates; EU-focused | Holistic AI has automated model cards, multiple regulations, compliance fast-lane |
| AI Score | Compliance-by-design; workflow validation; regulated enterprise focus | AI Score is simpler; lower price; basic governance |

## When to use

- You are a **regulated European enterprise** that wants to embed compliance into AI workflows
- You need **workflow templates** for common regulated enterprise workflows
- You want **design-phase compliance checks** rather than runtime compliance
- You have **limited budget** and cannot afford Tier A/B governance platforms
- You need **EU data residency** for compliance-by-design workflows

## When to avoid

- You are a **large enterprise** needing comprehensive governance (use Credo AI or Holistic AI)
- You need **runtime security** (prompt injection detection, jailbreak detection, content filtering)
- You need **enterprise compliance** (SOC 2, ISO 27001, NIST AI RMF)
- You need **advanced workflow customization** (Iridius templates are limited)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Iridius Compliance Platform (2026-06)
- **Last update:** Active; limited updates due to early-stage funding
- **Enterprise:** Self-service; basic enterprise support; budget-friendly pricing

## License

Content: CC BY 4.0  
Tool: Proprietary (Iridius)
