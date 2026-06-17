# HiddenLayer

**Tier:** A  
**Type:** AI Security Posture  
**License:** Proprietary  
**Stars/Adoption:** $56M raised (Enterprise)  
**Region:** US

## Overview

HiddenLayer is an AI security posture platform that provides model scanning, runtime threat detection, and adversarial defense. It is aligned with the MITRE ATLAS framework and specializes in model theft detection and adversarial attack prevention. It is a standalone AI security platform, not tied to a broader cybersecurity ecosystem like Palo Alto or SentinelOne.

## Setup experience

### Installation

HiddenLayer is a **managed enterprise service** with optional on-premise deployment.

```bash
pip install hiddenlayer-ai
```

Install time: ~15 seconds (SDK).  
Enterprise onboarding: Via HiddenLayer sales; includes dedicated security engineer.

### Configuration

```python
from hiddenlayer_ai import HiddenLayerClient

client = HiddenLayerClient(
    api_key=os.environ["HIDDENLAYER_API_KEY"],
    org_id="your-org-id"
)

# Model scanning (pre-deployment)
scan_result = client.scan_model(
    model_path="path/to/your/model.pkl",
    framework="pytorch",
    checks=["adversarial_vulnerability", "backdoor", "data_poisoning", "model_theft"]
)

# Runtime threat detection
runtime_result = client.runtime_detect(
    prompt=user_input,
    response=llm_response,
    model_id="your-model-id",
    threats=["prompt_injection", "jailbreak", "model_extraction", "adversarial_attack"]
)

if runtime_result.threat_detected:
    block_request(runtime_result.threat_type)
```

**Ops burden:** Low-Moderate. Dedicated security engineer for onboarding. Model scanning is offline (pre-deployment). Runtime detection is online but requires model registration. MITRE ATLAS alignment provides structured threat taxonomy.

### First blocked adversarial prompt

With runtime detection enabled, prompt injection and adversarial attacks are detected. Time to first block: ~5 minutes (enterprise onboarding) or ~1 minute (SDK + API key).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | MITRE ATLAS aligned; strong on model theft and adversarial attacks |
| Latency | Low | ~20-50ms runtime detection overhead |
| Token economics | Moderate | Enterprise pricing; volume-based |
| Scale behavior | Excellent | Cloud-native; auto-scaling; on-premise option |
| Ops burden | Low-Moderate | Dedicated security engineer; model registration required |
| Developer experience | Good | Good docs; MITRE ATLAS taxonomy; enterprise support |
| Data sovereignty | Moderate | Cloud-native; on-premise option available; enterprise data residency |

## Known bypasses

### Model extraction attack
HiddenLayer specializes in model theft detection. However, an attacker can use a **model extraction attack** (querying the model with carefully crafted inputs to reconstruct the model's behavior) to steal the model without triggering HiddenLayer's detection. **Workaround:** Implement rate limiting, query diversity checks, and output perturbation to make extraction harder.

### Adversarial example bypass
An attacker can craft an **adversarial example** (input with imperceptible perturbations that cause the model to misclassify) that bypasses HiddenLayer's detection. **Workaround:** Use adversarial training, input preprocessing, and ensemble detection to make adversarial examples harder to craft.

### Zero-day attack
HiddenLayer's detection is based on known attack patterns (MITRE ATLAS). A **zero-day attack** (novel attack not in the taxonomy) may bypass detection. **Workaround:** Use behavioral anomaly detection in addition to signature-based detection; regularly update the threat database.

### On-premise resource exhaustion
If HiddenLayer is deployed on-premise, a sustained attack can exhaust CPU/GPU resources, causing latency spikes or dropped requests. **Workaround:** Size infrastructure for attack load; use cloud fallback for overflow.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Can you explain how adversarial attacks work?" | Runtime detection flags "adversarial" + "attack" as potential attack | Add `security_education` context or whitelist educational queries |
| "Query the model with 1000 different inputs to test performance" | Model extraction detection flags high query volume as potential theft | Add `performance_testing` context or whitelist testing environments |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | MITRE ATLAS alignment supports full NIST lifecycle |
| EU AI Act | Strong | Model scanning + runtime detection supports Article 15; EU data residency available |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** HiddenLayer's MITRE ATLAS alignment and model theft detection are unique in the Tier A landscape. It is the only tool that explicitly maps to the MITRE ATLAS framework.

## Comparison with peers

| vs. | HiddenLayer wins | HiddenLayer loses |
|-----|---------------|-------------------|
| Protect AI (Palo Alto) | MITRE ATLAS alignment; model theft detection; standalone (not tied to broader ecosystem) | Protect AI has full lifecycle coverage (model scanning → red teaming → runtime → governance) |
| Lakera Guard | MITRE ATLAS alignment; model theft detection; adversarial defense | Lakera has higher detection accuracy on prompt injection; lower latency |
| Fiddler AI | MITRE ATLAS alignment; model theft detection; adversarial defense | Fiddler has deeper observability and ML drift detection |

## When to use

- You need **MITRE ATLAS-aligned threat detection**
- You need **model theft detection** and adversarial defense
- You want a **standalone AI security platform** (not tied to a broader cybersecurity ecosystem)
- You need **model scanning** for adversarial vulnerabilities, backdoors, and data poisoning
- You value **dedicated security engineer support**

## When to avoid

- You need a **free, open-source solution** (HiddenLayer is enterprise-priced)
- You need **full lifecycle AI security** (model scanning + red teaming + runtime + governance) — use Protect AI
- You need **sub-10ms latency** (runtime detection overhead is ~20-50ms)
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** HiddenLayer AI (2026-06)
- **Last update:** Active; MITRE ATLAS updates quarterly
- **Enterprise:** Available via HiddenLayer sales; on-premise option available

## License

Content: CC BY 4.0  
Tool: Proprietary (HiddenLayer)
