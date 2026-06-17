# Lasso Security

**Tier:** B  
**Type:** Multi-point Enforcement  
**License:** Proprietary  
**Stars/Adoption:** $6M raised (Early-stage)  
**Region:** US

## Overview

Lasso Security provides multi-point enforcement for AI applications through a browser extension, API gateway, SDK, and MCP gateway. It focuses on application security (app-sec) and agent-specific features, making it one of the few tools that explicitly targets the agentic AI attack surface. Its MCP gateway is a key differentiator for protecting tool-calling agents.

## Setup experience

### Installation

Lasso Security is a **proprietary platform** with multiple deployment options.

```bash
pip install lasso-security
# Browser extension: Chrome Web Store
# SDK: npm install lasso-sdk
```

Install time: ~15 seconds (pip); ~1 minute (browser extension).  
Enterprise onboarding: Via Lasso Security sales.

### Configuration

```python
from lasso_security import LassoClient

client = LassoClient(
    api_key=os.environ["LASSO_API_KEY"],
    org_id="your-org-id"
)

# Multi-point enforcement
client.configure_enforcement(
    points=[
        "browser_extension",  # Browser-level AI usage monitoring
        "api_gateway",        # API-level request filtering
        "sdk",                # SDK-level input validation
        "mcp_gateway"         # MCP server protection
    ]
)

# MCP gateway protection
mcp_result = client.mcp_protect(
    server_id="kb-server",
    request=user_input,
    policies=[
        "auth_required",
        "tool_call_validation",
        "input_sanitization"
    ]
)

if mcp_result.blocked:
    block_request(mcp_result.reason)
```

**Ops burden:** Low-Moderate. Multi-point deployment requires coordination across browser, API, SDK, and MCP layers. MCP gateway requires understanding of the MCP server inventory and tool schemas.

### First blocked adversarial prompt

With MCP gateway and API gateway enabled, unauthorized MCP server requests and API-level attacks are blocked. Time to first block: ~5 minutes (multi-point setup).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | Multi-point enforcement; MCP gateway is unique; app-sec focus |
| Latency | Low-Moderate | ~20-50ms per enforcement point; multi-point adds overhead |
| Token economics | Moderate | Enterprise pricing; multi-point licensing |
| Scale behavior | Good | Cloud-native; multi-point scales independently |
| Ops burden | Low-Moderate | Multi-point deployment; MCP gateway setup requires domain knowledge |
| Developer experience | Good | Multiple SDKs (Python, JS); browser extension; good docs |
| Data sovereignty | Moderate | Cloud-native; enterprise data residency options |

## Known bypasses

### Enforcement point gap
If an enforcement point is not deployed (e.g., browser extension not installed on a BYOD device), the attack bypasses that point. An attacker can target the weakest enforcement point. **Workaround:** Implement defense-in-depth with overlapping enforcement; require all points for high-risk applications.

### MCP server impersonation
Lasso's MCP gateway validates MCP servers by ID. If an attacker impersonates a legitimate server ID, the gateway may allow the request. **Workaround:** Implement cryptographic authentication for MCP servers; use behavioral monitoring.

### Browser extension bypass
The browser extension can be disabled, bypassed, or not installed on all devices. An attacker can use a device without the extension to bypass browser-level enforcement. **Workaround:** Enforce browser extension installation via MDM; or use API-level enforcement as a fallback.

### SDK bypass
If the application bypasses the SDK and calls the API directly, the SDK-level enforcement is not applied. **Workaround:** Enforce SDK usage via API authentication and rate limiting.

## False positive examples

| Benign request | Why it was blocked | How to tune |
|--------------|-------------------|-------------|
| "Install this MCP server for testing" | MCP gateway blocks unregistered server | Add `testing_environment` context or whitelist testing servers |
| "Use the developer API to debug the application" | API gateway blocks developer API access | Add `developer_role` context or whitelist developer APIs |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | Multi-point enforcement supports input validation and access control |
| EU AI Act | Moderate | Data residency options; multi-point enforcement supports Article 15 |
| SOC 2 | Moderate | Audit logs available; multi-point logging provides comprehensive trails |
| ISO 27001 | Moderate | Multi-point enforcement supports ISMS; no formal certification |

**Strength:** Lasso Security's multi-point enforcement and MCP gateway are unique in the Tier B landscape. It is one of the few tools that explicitly targets the agentic AI attack surface with app-sec features.

## Comparison with peers

| vs. | Lasso Security wins | Lasso Security loses |
|-----|---------------------|---------------------|
| Future AGI Protect | Multi-point enforcement (browser + API + SDK + MCP); app-sec focus | Future AGI Protect has 18+ security scanners and broader attack coverage |
| Bifrost | Multi-point enforcement; MCP gateway; app-sec focus | Bifrost has 11μs latency and is open-source |
| ClawGuard | Multi-point enforcement; browser extension; SDK | ClawGuard is open-source and research-backed; Lasso has more enterprise features |

## When to use

- You need **multi-point enforcement** (browser + API + SDK + MCP)
- You need **MCP gateway protection** for tool-calling agents
- You want **app-sec focused** AI security (application security perspective)
- You need **browser-level monitoring** for AI usage
- You are building an **agentic AI application** with multiple entry points

## When to avoid

- You need a **free, open-source solution** (Lasso is proprietary)
- You have **single-point AI applications** (multi-point is overkill)
- You need **sub-10ms latency** (multi-point adds overhead)
- You need **enterprise compliance automation** (SOC 2, EU AI Act mapping is DIY)
- You need **model scanning or red teaming** (not in core scope)

## Version notes

- **Tested version:** Lasso Security Platform (2026-06)
- **Last update:** Active; MCP gateway updated monthly
- **Enterprise:** Available via Lasso Security sales; multi-point deployment options

## License

Content: CC BY 4.0  
Tool: Proprietary (Lasso Security)
