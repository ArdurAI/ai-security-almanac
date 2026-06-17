# Capsule Security

**Tier:** C  
**Type:** Runtime Trust Layer  
**License:** Proprietary  
**Stars/Adoption:** $7M raised (Early-stage)  
**Region:** US

## Overview

Capsule Security is an early-stage runtime trust layer for AI agents, providing agent monitoring, control, and manipulation prevention. With $7M raised, it focuses on the emerging agentic AI attack surface — monitoring agent behavior, controlling agent actions, and preventing manipulation of agents by external actors. It is a niche tool that targets the intersection of AI security and agent orchestration.

## Setup experience

### Installation

Capsule Security is a **managed API service** with a lightweight SDK.

```bash
pip install capsule-security
```

Install time: ~10 seconds.  
API key setup: Self-service via web portal; basic enterprise support.

### Configuration

```python
from capsule_security import CapsuleClient

client = CapsuleClient(
    api_key=os.environ["CAPSULE_API_KEY"],
    org_id="your-org-id"
)

# Agent monitoring
client.monitor.agent(
    agent_id="customer-service-agent",
    metrics=[
        "action_count",
        "tool_call_count",
        "external_api_calls",
        "data_access_volume",
        "manipulation_indicators"
    ]
)

# Agent control
client.control.agent(
    agent_id="customer-service-agent",
    policies=[
        "max_tool_calls_per_session: 10",
        "max_external_api_calls_per_minute: 5",
        "max_data_access_volume_per_session: 100MB",
        "block_unauthorized_tool_calls: true"
    ]
)

# Manipulation prevention
prevention = client.prevent.manipulation(
    agent_id="customer-service-agent",
    checks=[
        "instruction_hierarchy_violation",
        "unauthorized_goal_modification",
        "external_influence_detection",
        "behavioral_anomaly_detection"
    ]
)

if prevention.manipulation_detected:
    block_agent(prevention.reason)
```

**Ops burden:** Low-Moderate. Lightweight API; pre-configured agent policies; basic monitoring. Limited customization; pre-defined manipulation checks. Enterprise support is minimal.

### First blocked adversarial prompt

With manipulation prevention enabled, unauthorized goal modifications and instruction hierarchy violations are detected. Time to first block: ~2 minutes from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate | Agent-specific checks; pre-configured manipulation detection; limited customization |
| Latency | Low | ~50-100ms API response |
| Token economics | Low | Budget-friendly pricing; per-agent API pricing |
| Scale behavior | Moderate | API service; may struggle with many agents due to early-stage infrastructure |
| Ops burden | Low-Moderate | Lightweight API; pre-configured policies; basic monitoring |
| Developer experience | Good | Simple API; agent-focused SDK; good docs for early-stage tool |
| Data sovereignty | Moderate | US-based; no EU data residency option; no self-hosted option |

## Known bypasses

### Agent scope limitation
Capsule Security monitors and controls agents but does not protect the underlying LLM or the tools the agent uses. An attacker can exploit the LLM or a tool directly, bypassing Capsule's controls. **Workaround:** Combine Capsule Security with LLM-level guardrails (e.g., Guardrails AI, Lakera Guard) and tool-level security (e.g., Future AGI Protect, ClawGuard).

### Monitoring blind spot
Capsule's monitoring is agent-centric. If the agent operates in an environment that Capsule cannot monitor (e.g., a local script, a container without Capsule integration), the monitoring is blind. **Workaround:** Enforce Capsule integration via deployment policies; or use network-level monitoring as a fallback.

### Policy bypass
An attacker can manipulate the agent to modify its own policies (e.g., "increase the max tool calls limit"). If the agent has sufficient autonomy, it may bypass Capsule's controls. **Workaround:** Implement immutable policies; or use a policy engine that cannot be modified by the agent.

### Funding risk
With $7M raised, Capsule Security is early-stage. There is a risk of limited development resources, slow feature updates, or platform changes. **Workaround:** Evaluate the vendor's roadmap; plan for migration if needed.

## False positive examples

| Benign agent behavior | Why it was flagged | How to tune |
|----------------------|-------------------|-------------|
| "Agent made 15 tool calls in a complex customer service session" | Agent control flags as "excessive tool calls" | Increase the max_tool_calls_per_session limit; or add `complex_session` context |
| "Agent accessed 150MB of customer data for a comprehensive report" | Agent control flags as "excessive data access" | Increase the max_data_access_volume_per_session limit; or add `comprehensive_report` context |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Agent monitoring supports basic input validation; no NIST mapping |
| EU AI Act | Moderate | Agent monitoring supports Article 15 (conformity with design); limited conformity documentation; no EU data residency |
| SOC 2 | Weak | No SOC 2 certification; early-stage |
| ISO 27001 | Weak | No ISO 27001 certification; early-stage |

**Gap:** Capsule Security is an early-stage agent security tool with limited features and funding. It is suitable for small organizations with basic agent monitoring and control needs but not for enterprise-scale agent security or comprehensive compliance.

## Comparison with peers

| vs. | Capsule Security wins | Capsule Security loses |
|-----|---------------------|------------------------|
| Future AGI Protect | Agent-focused; manipulation prevention; lightweight | Future AGI Protect has 18+ security scanners, MCP gateway, and broader attack coverage |
| ClawGuard | Agent-focused; manipulation prevention; lightweight | ClawGuard is open-source, research-backed, and has user-confirmed rule sets |
| Lasso Security | Agent-focused; manipulation prevention | Lasso Security has multi-point enforcement (browser + API + SDK + MCP) and enterprise features |

## When to use

- You are building **AI agents** and need basic monitoring and control
- You need **manipulation prevention** for agent behavior
- You want **agent-specific security** (not general-purpose LLM security)
- You have **limited budget** and cannot afford Tier A/B agent security tools
- You need **basic agent monitoring** (action count, tool calls, data access)

## When to avoid

- You are a **large enterprise** (Capsule Security is not enterprise-grade)
- You need **comprehensive LLM security** (prompt injection, jailbreak, content filtering)
- You need **enterprise compliance** (SOC 2, ISO 27001, NIST AI RMF, EU AI Act with data residency)
- You need **EU data residency** (Capsule is US-based)
- You need **MCP or agent tool-call protection** (Capsule monitors but does not protect tool calls)
- You need **advanced adversarial detection** (pre-configured checks are limited)

## Version notes

- **Tested version:** Capsule Security Runtime Trust Layer (2026-06)
- **Last update:** Active; limited updates due to early-stage funding
- **Enterprise:** Self-service; minimal enterprise support; budget-friendly pricing

## License

Content: CC BY 4.0  
Tool: Proprietary (Capsule Security)
