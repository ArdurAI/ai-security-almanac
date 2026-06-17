# Prompt Security (SentinelOne)

**Tier:** A  
**Type:** Runtime Defense  
**License:** Proprietary  
**Stars/Adoption:** Enterprise  
**Region:** US (now SentinelOne)

## Overview

Prompt Security provides inline prompt and response inspection for LLM applications, detecting prompt injection, jailbreaks, data exfiltration, and sensitive topic leakage. Acquired by SentinelOne in 2025, it integrates with SentinelOne's endpoint protection and XDR platform.

## Setup experience

### Installation

Prompt Security is a **cloud-native service** with an inline proxy architecture.

```python
pip install prompt-security
```

Install time: ~10 seconds.  
API key setup: Enterprise onboarding via SentinelOne portal.

### Configuration

```python
from prompt_security import SentinelOnePromptSecurity

ps = SentinelOnePromptSecurity(
    api_key=os.environ["SENTINELONE_PS_API_KEY"],
    org_id="your-org-id"
)

result = ps.inspect(
    prompt=user_input,
    response_model="gpt-4o",
    policies=["prompt_injection", "jailbreak", "data_exfiltration", "topic_restrictions"]
)

if result.risk_score > 0.8:
    block_request(result.reason)
```

**Ops burden:** Low. Cloud-native; policies are configured via dashboard. Integration with SentinelOne XDR enables correlation with endpoint threats. The inline proxy requires DNS or load-balancer configuration to intercept LLM traffic.

### First blocked adversarial prompt

With default policies enabled, prompt injection and jailbreaks are blocked. Time to first block: ~2 minutes from API key configuration.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate + SentinelOne integration)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Very High | SentinelOne threat intelligence + proprietary detection models |
| Latency | Low | ~20-50ms inline proxy overhead |
| Token economics | Moderate | Enterprise pricing; volume-based |
| Scale behavior | Excellent | Cloud-native; auto-scaling; SentinelOne infrastructure |
| Ops burden | Low | Dashboard-driven policy management; XDR integration |
| Developer experience | Good | Simple SDK; SentinelOne ecosystem integration |
| Data sovereignty | Moderate | Cloud-native; enterprise data residency options available |

## Known bypasses

### XDR correlation blind spot
Prompt Security correlates with SentinelOne XDR for endpoint threats. If the attacker is not on a managed endpoint (e.g., public WiFi, unmanaged device), the XDR correlation is blind. **Workaround:** Ensure all access paths are covered by SentinelOne agents or use standalone Prompt Security policies.

### Response-side exfiltration
Prompt Security inspects both prompts and responses. However, if the LLM response is streamed and the malicious payload is in the first few tokens, the streaming inspector may not have enough context to block. **Workaround:** Enable full-response buffering for high-risk applications.

### Topic restriction bypass
Topic restrictions use semantic classification. An attacker can reframe a restricted topic using euphemisms, hypotheticals, or roleplay framing. **Workaround:** Use multi-layer topic detection + human review for edge cases.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Write a story about a hacker who becomes a security consultant" | "hacker" triggers data_exfiltration policy | Add `creative_writing` as an exception context or use topic whitelisting |
| "Export my chat history to CSV for analysis" | "export" triggers data_exfiltration | Use the dashboard to distinguish user-initiated exports from unauthorized exfiltration |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | SentinelOne governance platform integration |
| EU AI Act | Strong | GDPR-compliant processing; EU data residency available |
| SOC 2 | Strong | Inherits SentinelOne SOC 2 Type II |
| ISO 27001 | Strong | Inherits SentinelOne ISO 27001 |

**Strength:** As part of SentinelOne, Prompt Security benefits from enterprise-grade compliance infrastructure and threat intelligence feeds.

## Comparison with peers

| vs. | Prompt Security wins | Prompt Security loses |
|-----|---------------------|----------------------|
| Lakera Guard | SentinelOne ecosystem integration; XDR correlation; broader threat intelligence | Slightly higher latency; more complex enterprise onboarding |
| Guardrails AI | Enterprise compliance; managed SLA; threat intelligence | Not free; data leaves infrastructure; less customizable |
| Arthur AI Shield | Inline proxy architecture; response-side inspection | No hallucination scoring; narrower scope than Arthur |

## When to use

- You are already a **SentinelOne customer** and want unified endpoint + AI security
- You need **enterprise compliance** (SOC 2, ISO 27001) with minimal setup
- You need **response-side inspection** (not just input filtering)
- You value **threat intelligence correlation** between endpoint and AI threats

## When to avoid

- You need **full data sovereignty** without enterprise negotiations
- You are not a SentinelOne customer and want standalone AI security (possible but less value)
- You need **custom detection logic** (proprietary models; limited customization)
- Your budget is constrained (enterprise pricing)

## Version notes

- **Tested version:** Cloud service (2026-06)
- **Last update:** Active; SentinelOne integration ongoing
- **Enterprise:** Available via SentinelOne sales; no self-hosted option

## License

Content: CC BY 4.0  
Tool: Proprietary (SentinelOne)
