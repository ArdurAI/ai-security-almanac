# WitnessAI

**Tier:** A  
**Type:** Network-level Governance  
**License:** Proprietary  
**Stars/Adoption:** $58M raised (Series B)  
**Region:** US

## Overview

WitnessAI is a network-level AI governance platform that provides behavioral intent analysis, policy routing, and network connector architecture. It sits at the network layer, intercepting AI traffic between applications and LLM providers, enabling policy enforcement without code changes. Its unique architecture allows it to govern AI usage across the entire enterprise network.

## Setup experience

### Installation

WitnessAI is a **network-level appliance** (virtual or physical) deployed at the network edge.

```bash
# No pip install — network appliance
# Deployed via VM image, container, or physical appliance
```

Setup time: ~30-60 minutes (network configuration, SSL/TLS interception setup, policy routing).  
Enterprise onboarding: Via WitnessAI sales; includes network architect.

### Configuration

```yaml
# witnessai-policy.yaml
network_connectors:
  - name: "openai-connector"
    target: "api.openai.com"
    policies:
      - name: "block-prompt-injection"
        type: "prompt_injection"
        action: "block"
      - name: "block-pii-exfiltration"
        type: "pii_exfiltration"
        action: "block"
      - name: "route-by-intent"
        type: "intent_analysis"
        action: "route"
        routes:
          - intent: "financial_advice"
            target: "compliant-llm-provider"
          - intent: "general_knowledge"
            target: "default-llm-provider"

behavioral_analysis:
  enabled: true
  baseline_period_days: 7
  anomaly_threshold: 2.0  # standard deviations from baseline
```

**Ops burden:** Moderate. Network-level deployment requires network architecture expertise. SSL/TLS interception requires certificate management. Policy routing requires understanding of intent categories and compliant LLM providers.

### First blocked adversarial prompt

With network-level policies enabled, prompt injection and PII exfiltration are blocked at the network layer. Time to first block: ~30-60 minutes (network setup) or ~0 minutes (if already deployed).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | Network-level detection; behavioral intent analysis is unique but may miss application-level attacks |
| Latency | Low-Moderate | ~10-30ms network overhead; SSL/TLS interception adds latency |
| Token economics | Moderate | Enterprise pricing; network appliance licensing |
| Scale behavior | Good | Network appliance scales with network infrastructure; may need multiple appliances for high traffic |
| Ops burden | Moderate | Network architecture setup; SSL/TLS certificate management; policy routing configuration |
| Developer experience | Moderate | No code changes required; but network setup is complex; good docs for network admins |
| Data sovereignty | Excellent | Network appliance runs on-premise; full data sovereignty; no external calls |

## Known bypasses

### SSL/TLS bypass
If the application uses certificate pinning or end-to-end encryption that WitnessAI cannot intercept, the network appliance cannot inspect the traffic. **Workaround:** Configure the application to use WitnessAI's CA certificate; or use application-level guardrails in addition to network-level.

### Direct API bypass
If the application bypasses the network (e.g., via a VPN, direct IP connection, or local LLM), WitnessAI cannot intercept the traffic. **Workaround:** Enforce network routing policies; use application-level guardrails as a fallback.

### Intent analysis false negative
WitnessAI's behavioral intent analysis is based on historical patterns. An attacker can craft a prompt that appears benign in the context of historical behavior but is malicious. **Workaround:** Use shorter baseline periods; combine with application-level detection.

### Policy routing bypass
If the policy routing is misconfigured, an attacker can route sensitive requests to a non-compliant LLM provider. **Workaround:** Regularly audit policy routing rules; use default-deny policies.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "My credit card is 1234-5678-9012-3456 for the purchase" | PII exfiltration filter flags the credit card number | Add `ecommerce` context or use PII anonymization instead of blocking |
| "I need financial advice on my 401k" | Intent analysis routes to compliant LLM provider (not a false positive, but a routing decision) | Tune intent categories or use a more nuanced routing policy |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Network-level governance supports full NIST lifecycle; behavioral analysis supports Govern |
| EU AI Act | Strong | Network-level data residency; policy routing supports Article 15; on-premise deployment = full data sovereignty |
| SOC 2 | Strong | SOC 2 Type II certified |
| ISO 27001 | Strong | ISO 27001 certified |

**Strength:** WitnessAI's network-level architecture is unique in the Tier A landscape. It provides governance without code changes, making it ideal for enterprises with many AI applications that cannot be modified individually.

## Comparison with peers

| vs. | WitnessAI wins | WitnessAI loses |
|-----|---------------|-----------------|
| Lakera Guard | Network-level governance; no code changes; behavioral intent analysis; full data sovereignty | Lakera has higher detection accuracy on prompt injection; lower latency; simpler setup |
| Protect AI (Palo Alto) | Network-level governance; no code changes; behavioral intent analysis | Protect AI has full lifecycle coverage (model scanning → red teaming → runtime → governance) |
| Fiddler AI | Network-level governance; no code changes; full data sovereignty | Fiddler has deeper observability and ML drift detection |

## When to use

- You need **network-level AI governance** without code changes
- You have **many AI applications** that cannot be modified individually
- You need **behavioral intent analysis** and policy routing
- You want **full data sovereignty** with on-premise deployment
- You need **SSL/TLS interception** for AI traffic inspection

## When to avoid

- You need a **free, open-source solution** (WitnessAI is enterprise-priced)
- You have **few AI applications** and can modify them individually (application-level guardrails are simpler)
- You need **sub-10ms latency** (network overhead + SSL/TLS interception adds latency)
- You need **MCP or agent tool-call protection** (not in core scope)
- Your applications use **certificate pinning** or end-to-end encryption that cannot be intercepted

## Version notes

- **Tested version:** WitnessAI Network Appliance (2026-06)
- **Last update:** Active; quarterly policy routing updates
- **Enterprise:** Available via WitnessAI sales; on-premise and virtual appliance options

## License

Content: CC BY 4.0  
Tool: Proprietary (WitnessAI)
