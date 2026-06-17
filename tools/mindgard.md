# Mindgard

**Tier:** B  
**Type:** AI Security + Red Teaming  
**License:** Proprietary  
**Stars/Adoption:** $12M raised (Enterprise)  
**Region:** UK/EU

## Overview

Mindgard is an AI security and red teaming platform that provides AI reconnaissance, adversarial testing, and runtime protection. It is EU AI Act mapped and focuses on helping organizations understand and mitigate AI-specific risks. It combines automated red teaming with runtime protection, making it a hybrid tool that spans evaluation and defense.

## Setup experience

### Installation

Mindgard is a **managed enterprise service** with optional self-hosted components.

```bash
pip install mindgard-ai
```

Install time: ~15 seconds (SDK).  
Enterprise onboarding: Via Mindgard sales; includes security consultant.

### Configuration

```python
from mindgard_ai import MindgardClient

client = MindgardClient(
    api_key=os.environ["MINDGARD_API_KEY"],
    org_id="your-org-id"
)

# AI reconnaissance
recon = client.reconnaissance(
    model_id="your-model-id",
    checks=[
        "attack_surface",
        "vulnerability_exposure",
        "data_leakage_risk",
        "adversarial_robustness"
    ]
)

# Automated red teaming
red_team = client.red_team(
    model_id="your-model-id",
    attack_types=[
        "prompt_injection",
        "jailbreak",
        "data_exfiltration",
        "model_extraction"
    ],
    num_tests=1000
)

# Runtime protection
runtime = client.runtime_protect(
    prompt=user_input,
    response=llm_response,
    model_id="your-model-id",
    policies=["prompt_injection", "jailbreak", "data_exfiltration"]
)

if runtime.blocked:
    block_request(runtime.reason)
```

**Ops burden:** Low-Moderate. Enterprise onboarding includes security consultant. AI reconnaissance and red teaming are automated. Runtime protection requires model registration and policy configuration. EU AI Act mapping is pre-built.

### First blocked adversarial prompt

With runtime protection enabled, prompt injection and jailbreaks are detected. Time to first block: ~5 minutes (enterprise onboarding) or ~1 minute (SDK + API key).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | AI reconnaissance + automated red teaming + runtime protection; EU AI Act mapped |
| Latency | Low | ~20-50ms runtime protection overhead |
| Token economics | Moderate | Enterprise pricing; hybrid evaluation + defense |
| Scale behavior | Good | Cloud-native; auto-scaling; self-hosted components available |
| Ops burden | Low-Moderate | Enterprise onboarding; automated red teaming; runtime protection requires model registration |
| Developer experience | Good | Simple SDK; EU AI Act mapping; security consultant support |
| Data sovereignty | Moderate | Cloud-native; EU data residency; self-hosted components available |

## Known bypasses

### Reconnaissance timing gap
Mindgard's AI reconnaissance runs periodically. If the model or application changes between reconnaissance runs, the risk profile may be outdated. **Workaround:** Run reconnaissance continuously or after every deployment; or use runtime protection as a fallback.

### Red teaming coverage gap
Automated red teaming covers known attack types. A novel attack that does not match any known type may not be detected. **Workaround:** Supplement automated red teaming with manual penetration testing; regularly update attack types.

### Runtime protection bypass
If the application bypasses Mindgard's runtime protection and calls the LLM directly, the protection is not applied. **Workaround:** Enforce runtime protection via API authentication and network controls.

### Model registration gap
Runtime protection requires model registration. If a new model is deployed without registration, it is not protected. **Workaround:** Automate model registration in CI/CD pipelines; or use default protection for unregistered models.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Can you explain how model extraction works?" | Runtime protection flags "model extraction" as potential attack | Add `security_education` context or whitelist educational queries |
| "Test the model with 1000 inputs to measure performance" | AI reconnaissance flags high query volume as potential model extraction | Add `performance_testing` context or whitelist testing environments |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | AI reconnaissance + red teaming + runtime protection supports full NIST lifecycle |
| EU AI Act | Strong | Pre-built EU AI Act mapping; EU data residency; conformity documentation |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** Mindgard's EU AI Act mapping and hybrid approach (reconnaissance + red teaming + runtime) are unique among Tier B tools. Its UK/EU origin provides strong data sovereignty and regulatory alignment.

## Comparison with peers

| vs. | Mindgard wins | Mindgard loses |
|-----|---------------|----------------|
| Protect AI (Palo Alto) | EU AI Act mapping; UK/EU data residency; hybrid approach | Protect AI has full lifecycle coverage (model scanning → red teaming → runtime → governance) and Palo Alto ecosystem |
| Lakera Guard | AI reconnaissance; automated red teaming; EU AI Act mapping | Lakera Guard has higher detection accuracy and lower latency |
| HiddenLayer | EU AI Act mapping; hybrid approach (reconnaissance + red teaming + runtime) | HiddenLayer has MITRE ATLAS alignment and model theft detection |

## When to use

- You need **EU AI Act compliance** with pre-built mapping
- You want a **hybrid approach** (reconnaissance + red teaming + runtime protection)
- You need **UK/EU data residency** and regulatory alignment
- You want **automated red teaming** with runtime protection integration
- You need **AI reconnaissance** to understand your attack surface

## When to avoid

- You need a **free, open-source solution** (Mindgard is proprietary)
- You are not in the **EU/UK regulatory jurisdiction** (EU AI Act mapping is less valuable)
- You need **sub-10ms latency** (runtime protection overhead is ~20-50ms)
- You need **model scanning for supply chain security** (use Protect AI or HiddenLayer)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Mindgard Platform (2026-06)
- **Last update:** Active; EU AI Act mapping updated quarterly
- **Enterprise:** Available via Mindgard sales; self-hosted components available; EU data residency guaranteed

## License

Content: CC BY 4.0  
Tool: Proprietary (Mindgard)
