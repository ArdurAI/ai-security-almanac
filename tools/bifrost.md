# Bifrost

**Tier:** B  
**Type:** AI Gateway  
**License:** Apache 2.0  
**Stars/Adoption:** Open Source (Growing)  
**Region:** Global

## Overview

Bifrost is an open-source AI gateway designed for high-performance request routing, virtual key management, and guardrail integration. It claims **11μs overhead** per request — the lowest latency in the almanac. It includes MCP tool filtering, RBAC, SSO, vault integration, and virtual key management. It is not a security tool per se but a gateway that can enforce security policies at the routing layer.

## Setup experience

### Installation

```bash
pip install bifrost-gateway
# or
docker pull bifrost/gateway
```

Install time: ~10 seconds (pip) or instant (Docker).  
Docker image: ~50MB; minimal dependencies.

### Configuration

```yaml
# bifrost-config.yaml
gateway:
  port: 8080
  virtual_keys:
    - name: "openai-key"
      provider: "openai"
      key: "${OPENAI_API_KEY}"
      rate_limit: 1000  # requests per minute
  
  mcp_filter:
    enabled: true
    allowed_servers:
      - "kb-server"
      - "crm-server"
    blocked_servers:
      - "unauthorized-server"
  
  rbac:
    enabled: true
    roles:
      - name: "admin"
        permissions: ["*"]
      - name: "user"
        permissions: ["read", "chat"]
  
  sso:
    enabled: true
    provider: "oidc"
    client_id: "${OIDC_CLIENT_ID}"
    client_secret: "${OIDC_CLIENT_SECRET}"
  
  vault:
    enabled: true
    provider: "hashicorp"
    address: "${VAULT_ADDR}"
```

**Ops burden:** Low-Moderate. YAML configuration is straightforward. RBAC, SSO, and vault integration require enterprise infrastructure setup. MCP filter requires knowledge of the MCP server inventory.

### First blocked adversarial prompt

Bifrost does not detect prompt injection or adversarial content directly. It blocks based on **virtual key permissions**, **RBAC**, and **MCP server allow-lists**. An unauthorized MCP server request or an RBAC violation is blocked. Time to first block: ~1 minute from configuration.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | N/A | Gateway tool; does not detect adversarial content directly |
| Latency | Extremely Low | **11μs overhead** — the lowest in the almanac |
| Token economics | Very low | Self-hosted; no per-request cost beyond infrastructure |
| Scale behavior | Excellent | Gateway architecture; horizontal scaling; minimal overhead |
| Ops burden | Low-Moderate | YAML config; RBAC/SSO/vault setup for enterprise |
| Developer experience | Good | Simple API; good docs; Kubernetes manifests available |
| Data sovereignty | Excellent | Fully self-hosted; no external calls |

## Known bypasses

### Authorized but malicious MCP server
Bifrost's MCP filter allows or blocks servers by name. If an authorized server (e.g., "kb-server") is compromised, Bifrost will still route traffic to it. **Workaround:** Implement behavioral monitoring for authorized MCP servers; or use a dedicated MCP security tool (e.g., Future AGI Protect, ClawGuard).

### RBAC privilege escalation
If an attacker compromises an account with higher RBAC permissions, they can bypass the gateway's access controls. **Workaround:** Implement MFA, least-privilege access, and regular RBAC audits.

### Virtual key leak
If a virtual key is leaked, an attacker can use it to bypass the gateway's rate limiting and routing. **Workaround:** Rotate virtual keys regularly; use vault integration for secure key management.

### Gateway bypass
If the application bypasses the gateway and calls the LLM provider directly, Bifrost's controls are not applied. **Workaround:** Enforce gateway usage via network policies and IAM controls.

## False positive examples

Bifrost does not have false positives in the traditional sense — it is a gateway, not a content detector. However, misconfigured RBAC or MCP filters can block legitimate requests.

| Benign request | Why it was blocked | How to tune |
|--------------|-------------------|-------------|
| "Query the new knowledge base server" | MCP filter blocks unknown server | Add the new server to the allowed_servers list |
| "Admin wants to change the system configuration" | RBAC blocks admin role from system config | Review and update RBAC permissions |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Gateway-level access control supports basic input validation; no governance or management |
| EU AI Act | Weak | Self-hosted = data sovereignty; no built-in compliance mapping |
| SOC 2 | Moderate | Vault integration supports key management; RBAC supports access controls; audit logs available |
| ISO 27001 | Moderate | Access control supports ISMS; no formal certification |

**Gap:** Bifrost is a gateway, not a security tool. It provides access control and routing but does not detect adversarial content, prompt injection, or jailbreaks. Combine with a dedicated security tool for full protection.

## Comparison with peers

| vs. | Bifrost wins | Bifrost loses |
|-----|-------------|---------------|
| Future AGI Protect | **11μs latency** (vs. 65ms); virtual key management; RBAC; SSO | Future AGI Protect has 18+ security scanners and prompt injection detection |
| Guardrails AI | **11μs latency**; gateway architecture; RBAC | Guardrails AI has output validation, PII detection, and 70+ Hub validators |
| Lakera Guard | **11μs latency**; self-hosted; no per-request cost | Lakera Guard has prompt injection detection, threat intelligence, and enterprise compliance |

## When to use

- You need **ultra-low-latency request routing** (~11μs overhead)
- You need **virtual key management** and rate limiting
- You need **MCP server filtering** at the gateway layer
- You need **RBAC, SSO, and vault integration** for enterprise access control
- You want a **self-hosted gateway** with minimal ops overhead

## When to avoid

- You need **prompt injection detection** or adversarial security (Bifrost is a gateway, not a security tool)
- You need **jailbreak detection** or system prompt protection
- You need **content moderation** or PII detection (use a dedicated security tool)
- You need **enterprise compliance automation** (SOC 2, EU AI Act mapping is DIY)

## Version notes

- **Tested version:** Bifrost 1.0.x (2026-06)
- **Last update:** Active; monthly releases
- **Community:** Apache 2.0; growing community; Kubernetes-native

## License

Content: CC BY 4.0  
Tool: Apache 2.0 (Open Source)
