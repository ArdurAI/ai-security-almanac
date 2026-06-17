# Protect AI (Palo Alto / Prisma AIRS)

**Tier:** A  
**Type:** Full Lifecycle Security  
**License:** Proprietary  
**Stars/Adoption:** $108M raised (Enterprise)  
**Region:** US

## Overview

Protect AI (now part of Palo Alto Networks, operating as Prisma AIRS) is a full-lifecycle AI security platform that covers model scanning, red teaming, runtime firewall, and governance. It is the most comprehensive AI security platform in the almanac, spanning from development (model scanning, NB Defense) to runtime (AI firewall) to governance (compliance, audit trails). Acquired by Palo Alto in 2025, it now integrates with Prisma Cloud and Cortex XDR.

## Setup experience

### Installation

Protect AI is a **managed enterprise service** with optional self-hosted components.

```bash
pip install protect-ai
```

Install time: ~15 seconds (SDK).  
Enterprise onboarding: Via Palo Alto Networks sales; includes dedicated onboarding engineer.

### Configuration

```python
from protect_ai import ProtectAIClient

client = ProtectAIClient(
    api_key=os.environ["PROTECT_AI_API_KEY"],
    org_id="your-org-id"
)

# Full lifecycle security pipeline
pipeline = client.create_pipeline(
    name="production-pipeline",
    stages=[
        "model_scanning",      # NB Defense: scan models for vulnerabilities
        "red_teaming",         # Automated red teaming before deployment
        "runtime_firewall",    # AI firewall for production traffic
        "governance"           # Compliance, audit trails, policy management
    ]
)

# Runtime firewall
firewall_result = client.runtime_firewall.inspect(
    prompt=user_input,
    response=llm_response,
    policies=["prompt_injection", "jailbreak", "data_exfiltration", "pii"]
)

if firewall_result.blocked:
    block_request(firewall_result.reason)
```

**Ops burden:** Low-Moderate. Palo Alto provides dedicated onboarding. The full lifecycle pipeline requires configuration of multiple stages (model scanning, red teaming, runtime firewall, governance). Each stage has pre-configured defaults but requires tuning for the specific application.

### First blocked adversarial prompt

With the runtime firewall enabled, prompt injection and jailbreaks are blocked. Time to first block: ~5 minutes (enterprise onboarding) or ~1 minute (SDK + API key).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Very High | Full lifecycle: model scanning + red teaming + runtime firewall + governance |
| Latency | Low | ~20-50ms runtime firewall overhead; model scanning is offline |
| Token economics | Moderate-High | Enterprise pricing; full lifecycle platform |
| Scale behavior | Excellent | Palo Alto infrastructure; auto-scaling; Cortex XDR integration |
| Ops burden | Low-Moderate | Dedicated onboarding; pre-configured defaults; full lifecycle requires more config than single-purpose tools |
| Developer experience | Good | Palo Alto ecosystem integration; good docs; enterprise support |
| Data sovereignty | Moderate | Cloud-native; enterprise data residency options; self-hosted components available |

## Known bypasses

### Model scanning timing gap
Protect AI's model scanning runs before deployment. If a model is updated after deployment (e.g., via in-context learning, fine-tuning, or prompt injection that changes the model's behavior), the pre-deployment scan may not catch new vulnerabilities. **Workaround:** Run continuous model scanning in production; or use runtime monitoring to detect behavioral changes.

### Red teaming coverage gap
Protect AI's red teaming is automated but may not cover novel attack patterns. An attacker can craft an attack that bypasses both the red teaming tests and the runtime firewall. **Workaround:** Supplement automated red teaming with manual penetration testing; regularly update red teaming tests.

### Palo Alto ecosystem dependency
Protect AI integrates deeply with Prisma Cloud and Cortex XDR. If the organization is not already a Palo Alto customer, the full value of the ecosystem integration is not realized. **Workaround:** Use Protect AI as a standalone platform; or adopt Palo Alto's broader security portfolio.

### Governance policy drift
Protect AI's governance module requires ongoing policy management. If policies are not updated as the application evolves, compliance gaps may emerge. **Workaround:** Implement regular policy reviews; use automated policy drift detection.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Scan this model for vulnerabilities" | Runtime firewall flags "scan" + "vulnerabilities" as potential attack | Add `security_operations` context or whitelist security team operations |
| "Red team this application" | Red teaming module flags this as a potential unauthorized test | Add `authorized_red_team` context or whitelist red team operations |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Full lifecycle coverage: Govern, Map, Measure, Manage |
| EU AI Act | Strong | Full lifecycle compliance; EU data residency; conformity documentation |
| SOC 2 | Strong | Inherits Palo Alto SOC 2 Type II |
| ISO 27001 | Strong | Inherits Palo Alto ISO 27001 certification |

**Strength:** Protect AI is the most comprehensive AI security platform in the almanac. Its full lifecycle coverage (model scanning → red teaming → runtime firewall → governance) is unique. The Palo Alto acquisition provides enterprise-grade compliance infrastructure.

## Comparison with peers

| vs. | Protect AI wins | Protect AI loses |
|-----|---------------|------------------|
| HiddenLayer | Full lifecycle (model scanning + runtime + governance); Palo Alto ecosystem | HiddenLayer has stronger MITRE ATLAS alignment and model theft detection |
| Lakera Guard | Full lifecycle; model scanning; governance; Palo Alto ecosystem | Lakera has higher detection accuracy on prompt injection; lower latency |
| Fiddler AI | Full lifecycle; model scanning; runtime firewall; governance | Fiddler has deeper observability and ML drift detection |

## When to use

- You need **full lifecycle AI security** (model scanning → red teaming → runtime → governance)
- You are already a **Palo Alto Networks customer** and want unified security
- You need **enterprise compliance** (SOC 2, ISO 27001, NIST AI RMF, EU AI Act) with minimal effort
- You need **model scanning** (NB Defense) for supply chain security
- You want **dedicated onboarding and enterprise support**

## When to avoid

- You need a **free, open-source solution** (Protect AI is enterprise-priced)
- You are not a Palo Alto customer and want a **standalone AI security tool** (possible but less value)
- You need **sub-10ms latency** (runtime firewall overhead is ~20-50ms)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Prisma AIRS (2026-06)
- **Last update:** Active; Palo Alto integration ongoing
- **Enterprise:** Available via Palo Alto Networks sales; self-hosted components available

## License

Content: CC BY 4.0  
Tool: Proprietary (Palo Alto Networks)
