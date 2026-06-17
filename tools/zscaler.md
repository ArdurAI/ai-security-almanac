# Zscaler

**Tier:** B  
**Type:** Zero Trust AI Security  
**License:** Proprietary  
**Stars/Adoption:** Enterprise (SSE leader)  
**Region:** Global

## Overview

Zscaler is a leading SSE (Security Service Edge) platform that has extended its zero-trust architecture to AI security. It provides CASB (Cloud Access Security Broker) shadow-IT discovery, AI Guard for content filtering, and MCP Gateway for agent tool-call protection. Announced MCP support in 2026, making it one of the first SSE platforms to explicitly address the agentic AI attack surface at the network level.

## Setup experience

### Installation

Zscaler is a **cloud-native SSE platform** — no installation required. Deployed via Zscaler Client Connector (ZCC) on endpoints or inline via network proxy.

```bash
# Zscaler Client Connector
# Deployed via MDM (Mobile Device Management) or manual install
# No pip install — network-level enforcement
```

Setup time: ~30-60 minutes (enterprise network configuration).  
Enterprise onboarding: Via Zscaler sales; includes network architect.

### Configuration

```yaml
# Zscaler AI Security Policy
ai_security:
  casb:
    enabled: true
    shadow_it_discovery: true
    ai_app_catalog: true  # ~1,000 AI tools catalog
  
  ai_guard:
    enabled: true
    content_filtering:
      - category: "prompt_injection"
        action: "block"
      - category: "jailbreak"
        action: "block"
      - category: "pii_exfiltration"
        action: "block"
    
  mcp_gateway:
    enabled: true
    allowed_mcp_servers:
      - "kb-server"
      - "crm-server"
    blocked_mcp_servers:
      - "*"  # default deny
    
  zero_trust:
    enabled: true
    device_trust: true
    user_trust: true
    app_trust: true
```

**Ops burden:** Moderate. SSE platform configuration requires network architecture expertise. ZCC deployment requires MDM or endpoint management. AI Guard and MCP Gateway policies require understanding of AI application traffic patterns.

### First blocked adversarial prompt

With AI Guard and MCP Gateway enabled, prompt injection and unauthorized MCP server requests are blocked at the network level. Time to first block: ~30-60 minutes (network configuration) or ~0 minutes (if already deployed).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | SSE-native; AI Guard is good but not cutting-edge; MCP Gateway is early |
| Latency | Very Low | Network-level enforcement; ~5-15ms overhead; SSE-native |
| Token economics | Moderate | Enterprise SSE pricing; included in Zscaler platform |
| Scale behavior | Excellent | SSE-native; auto-scales with Zscaler infrastructure |
| Ops burden | Moderate | SSE platform configuration; network architecture expertise required |
| Developer experience | Moderate | No code changes required; network-level enforcement; Zscaler ecosystem |
| Data sovereignty | Good | SSE data centers globally; EU data residency available |

## Known bypasses

### ZCC bypass
If Zscaler Client Connector is not installed on a device (e.g., BYOD, unmanaged device, VPN bypass), the network-level enforcement is not applied. **Workaround:** Enforce ZCC installation via MDM; use inline proxy for unmanaged devices; or implement application-level guardrails as a fallback.

### VPN bypass
An attacker can use a VPN to bypass Zscaler's network-level enforcement. If the VPN routes traffic outside the Zscaler network, AI Guard and MCP Gateway are not applied. **Workaround:** Enforce Zscaler tunneling for all traffic; block unauthorized VPNs; or implement application-level guardrails.

### AI Guard semantic bypass
Zscaler's AI Guard uses content filtering. An attacker can reframe harmful content using euphemisms, hypotheticals, or roleplay. **Workaround:** Combine with application-level semantic guardrails; or use a dedicated prompt injection detector.

### MCP Gateway early-stage limitation
Zscaler's MCP Gateway was announced in 2026 and is early-stage. It may not have comprehensive MCP server coverage or advanced authentication. **Workaround:** Monitor MCP Gateway updates; supplement with application-level MCP security (e.g., Future AGI Protect, ClawGuard).

## False positive examples

| Benign request | Why it was blocked | How to tune |
|--------------|-------------------|-------------|
| "Use the new AI tool for data analysis" | CASB shadow-IT discovery flags the new AI tool as unauthorized | Add the new AI tool to the allowed AI app catalog |
| "Connect to the testing MCP server" | MCP Gateway blocks unregistered server | Add `testing_environment` context or whitelist testing servers |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | SSE platform supports full NIST lifecycle; zero-trust architecture |
| EU AI Act | Strong | SSE data centers in EU; EU data residency; CASB supports Article 15 |
| SOC 2 | Strong | Inherits Zscaler SOC 2 Type II |
| ISO 27001 | Strong | Inherits Zscaler ISO 27001 certification |

**Strength:** Zscaler's SSE-native approach and zero-trust architecture provide comprehensive network-level AI security. The MCP Gateway announcement in 2026 positions it as a leader in SSE-based AI security.

## Comparison with peers

| vs. | Zscaler wins | Zscaler loses |
|-----|-------------|---------------|
| Netskope | MCP Gateway; AI Guard; zero-trust architecture; CASB shadow-IT | Netskope has broader AI traffic inspection and SaaS coverage |
| Palo Alto (Prisma AIRS) | SSE-native; zero-trust; CASB; MCP Gateway | Palo Alto has full lifecycle AI security (model scanning → red teaming → runtime → governance) |
| Bifrost | SSE-native; zero-trust; CASB; no code changes required | Bifrost is open-source and has 11μs latency |

## When to use

- You are already a **Zscaler customer** and want unified SSE + AI security
- You need **network-level AI governance** without code changes
- You need **CASB shadow-IT discovery** for AI applications
- You need **MCP Gateway protection** for tool-calling agents
- You want **zero-trust architecture** for AI traffic

## When to avoid

- You are not a Zscaler customer and want **standalone AI security** (possible but less value)
- You need **application-level guardrails** with fine-grained control (Zscaler is network-level)
- You need **sub-5ms latency** (SSE overhead is ~5-15ms)
- You need **model scanning or red teaming** (not in core scope)
- Your organization has **limited SSE infrastructure** (Zscaler requires SSE deployment)

## Version notes

- **Tested version:** Zscaler AI Security (2026-06)
- **Last update:** MCP Gateway announced 2026; AI Guard updated quarterly
- **Enterprise:** Available via Zscaler sales; SSE platform required

## License

Content: CC BY 4.0  
Tool: Proprietary (Zscaler)
