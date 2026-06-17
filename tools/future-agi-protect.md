# Future AGI Protect

**Tier:** A  
**Type:** Gateway + Guardrails  
**License:** Apache 2.0 (gateway)  
**Stars/Adoption:** 18+ scanners  
**Region:** Global

## Overview

Future AGI Protect is an open-source AI gateway with built-in guardrails that provides 18+ security scanners for text and image inputs. It specializes in agent tool-call rails and MCP server protection, with latency targets of 65ms for text and 107ms for image processing.

## Setup experience

### Installation

```bash
pip install future-agi-protect
# or docker pull futureagi/protect-gateway
```

Install time: ~20 seconds (pip) or instant (Docker).  
Docker image: ~150MB; includes all scanners.

### Configuration

```yaml
# protect-config.yaml
gateway:
  port: 8080
  scanners:
    - prompt_injection
    - pii_detection
    - toxicity
    - encoding_bypass
    - agent_tool_call_validation
    - mcp_server_authentication

policy:
  block_on:
    - prompt_injection: score > 0.8
    - pii_detection: score > 0.7
    - toxicity: score > 0.9
  agent_rails:
    validate_tool_calls: true
    mcp_auth_required: true
```

```python
from future_agi_protect import ProtectGateway

gateway = ProtectGateway(config="protect-config.yaml")
result = gateway.validate(
    text=user_input,
    context={"agent_id": "customer-service-bot", "mcp_servers": ["kb-server", "crm-server"]}
)
```

**Ops burden:** Low-Moderate. YAML configuration is straightforward. The agent tool-call and MCP validation require understanding your agent's tool schema. First-time setup for MCP protection: ~30 minutes.

### First blocked adversarial prompt

With the default scanners enabled, prompt injection and encoding bypasses are detected. Time to first block: ~3 minutes from `pip install`.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | High | 18+ scanners cover broad attack surface; strong on encoding bypasses |
| Latency | Low | 65ms text / 107ms image (self-hosted) |
| Token economics | Very low | Self-hosted; no per-request API costs |
| Scale behavior | Good | Gateway architecture; horizontal scaling supported |
| Ops burden | Low-Moderate | YAML config is simple; MCP setup requires domain knowledge |
| Developer experience | Good | Good docs; Docker-first; Kubernetes manifests available |
| Data sovereignty | Excellent | Fully self-hosted; no external calls |

## Known bypasses

### Agent tool-call schema manipulation
If the agent's tool schema is overly permissive, an attacker can craft a prompt that triggers a tool call with malicious parameters. Future AGI validates tool calls, but if the schema itself is vulnerable, the validation passes. **Workaround:** Audit and harden tool schemas independently; use Future AGI's schema linting feature.

### MCP server impersonation
An attacker can register an MCP server with a legitimate-sounding name but malicious behavior. Future AGI's MCP authentication check validates the server, but if the server is legitimately registered and later compromised, the check passes. **Workaround:** Implement MCP server behavior monitoring (not just authentication).

### Image-based prompt injection
Multimodal attacks that embed instructions in images may bypass text-only scanners. Future AGI has image scanning, but the 107ms latency target may require reducing image resolution, which can miss subtle attacks. **Workaround:** Use full-resolution image scanning for high-risk applications.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Execute the refund tool for order #12345" | "execute" + "tool" triggers agent tool-call validation | Add `refund_tool` to the allow-list in agent_rails config |
| "[Base64 string of a corporate logo]" | Encoding scanner flags any Base64 as suspicious | Add `image_data` context to skip encoding detection for known image fields |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Partial | Gateway architecture supports Map/Measure; no built-in risk assessment |
| EU AI Act | Partial | PII + toxicity scanners support Article 15; no conformity documentation |
| SOC 2 | Partial | Audit logs are gateway-level; no built-in SOC 2 control mapping |
| ISO 27001 | Partial | Input validation supports ISMS; no formal certification |

**Gap:** Compliance automation is not a primary focus. The open-source gateway provides the primitives; enterprise compliance is DIY.

## Comparison with peers

| vs. | Future AGI Protect wins | Future AGI Protect loses |
|-----|------------------------|--------------------------|
| Guardrails AI | MCP + agent tool-call protection; lower latency | Smaller validator ecosystem; less mature output validation |
| Lakera Guard | Self-hosted; no per-request cost; MCP protection | Lower detection rate on novel attacks (no threat intelligence feed) |
| Bifrost | More security scanners; broader attack coverage | Bifrost is faster (11μs) and has more enterprise features (RBAC, SSO) |

## When to use

- You need **MCP server protection** or agent tool-call validation
- You want a **self-hosted gateway** with broad scanner coverage
- You need **encoding bypass detection** (Base64, rot13, Unicode, etc.)
- You are building an **agentic AI application** with tool-calling

## When to avoid

- You need **enterprise compliance automation** out of the box
- You need **sub-10ms latency** (65ms is good but not ultra-low)
- You need **complex dialog flow control** (Future AGI is a gateway, not a dialog engine)
- You need **threat intelligence feeds** for novel attack detection

## Version notes

- **Tested version:** 1.2.x (latest stable as of 2026-06)
- **Last push:** Active; monthly scanner updates
- **Community:** Growing; Apache 2.0 encourages contributions

## License

Content: CC BY 4.0  
Tool: Apache 2.0 (gateway)
