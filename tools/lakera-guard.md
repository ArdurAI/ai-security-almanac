# Lakera Guard (Check Point)

**Tier:** A  
**Type:** Runtime Firewall  
**License:** Proprietary  
**Stars/Adoption:** Enterprise  
**Region:** Switzerland / Global (now Check Point)

## Overview

Lakera Guard is a real-time API firewall for LLM applications that detects prompt injection, jailbreaks, PII leakage, and data exfiltration. It is the team behind the **Gandalf** prompt injection benchmark dataset. Acquired by Check Point in 2024, it now integrates with Check Point's broader cybersecurity portfolio.

## Setup experience

### Installation

Lakera Guard is a **cloud API** with a self-hosted option for enterprise customers.

```python
pip install lakera
```

Install time: ~10 seconds.  
API key setup: Sign up at lakera.ai, copy API key to environment variable.

### Configuration

```python
from lakera import LakeraGuard

guard = LakeraGuard(api_key=os.environ["LAKERA_API_KEY"])

result = guard.detect(
    text=user_input,
    detectors=["prompt_injection", "jailbreak", "pii"]
)

if result.is_detected:
    raise SecurityException(result.detection_type)
```

**Ops burden:** Very low. One API call per request; no policy configuration required. The default detectors are tuned for production. Enterprise customers can configure custom policies via dashboard.

### First blocked adversarial prompt

With the default detectors enabled, known jailbreaks and prompt injections are blocked. Time to first block: ~1 minute from API key creation.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate + published claims)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Very High | Gandalf dataset authors; strong on prompt injection and jailbreaks |
| Latency | Very Low | Sub-50ms API response; self-hosted option <20ms |
| Token economics | Low-Moderate | Cloud API pricing per request; self-hosted is license-based |
| Scale behavior | Excellent | Cloud auto-scales; enterprise SLAs available |
| Ops burden | Very Low | Zero-config default; dashboard for custom policies |
| Developer experience | Excellent | Simple API; good docs; SDK for Python/JS |
| Data sovereignty | Moderate | Cloud API sends data to Lakera; self-hosted option available |

## Known bypasses

### Multi-turn trust building
Lakera's default detectors analyze single-turn requests. A multi-turn attack that builds rapport over 3-5 turns before injecting the payload may bypass detection on the final turn if the payload itself is not flagged. **Workaround:** Enable conversation-level analysis (enterprise feature) or implement application-level conversation history scanning.

### Novel encoding bypasses
While Lakera detects common encoding (Base64, rot13), novel encoding schemes or polyglot payloads may bypass. **Workaround:** Add a pre-processing layer that normalizes encoding before sending to Lakera.

### Indirect injection via tool results
Lakera focuses on direct input validation. If an agent retrieves a poisoned document and the malicious instruction is in the tool result (not the user prompt), Lakera may not detect it. **Workaround:** Scan tool results through Lakera before passing to the LLM.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "How do I bypass my company's firewall for legitimate testing?" | "bypass" + "firewall" triggers security-related detection | Tune threshold or add domain-specific exceptions |
| "Summarize this email: [contains technical security terms]" | Security jargon triggers PII/jailbreak heuristics | Use the dashboard to whitelist security domains |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Strong | Check Point integration provides broader risk management |
| EU AI Act | Strong | GDPR-compliant data processing; EU data residency available |
| SOC 2 | Strong | SOC 2 Type II certified via Check Point |
| ISO 27001 | Strong | Check Point maintains ISO 27001 certification |

**Strength:** As part of Check Point, Lakera inherits enterprise compliance infrastructure that pure open-source tools lack.

## Comparison with peers

| vs. | Lakera Guard wins | Lakera Guard loses |
|-----|------------------|--------------------|
| Guardrails AI | Higher detection accuracy; lower ops burden; enterprise compliance | Not free; data leaves your infrastructure (cloud) |
| NeMo Guardrails | Easier setup; no DSL learning curve; managed SLAs | Less multi-turn conversation control; not open-source |
| Arthur AI Shield | Gandalf dataset credibility; lower latency | Narrower scope (no hallucination scoring) |

## When to use

- You need **production-grade prompt injection defense** with minimal setup
- You want **enterprise compliance** (SOC 2, ISO 27001) out of the box
- You need **sub-50ms latency** with auto-scaling
- You value **threat intelligence** (Gandalf dataset, ongoing research)

## When to avoid

- You need **full data sovereignty** (air-gapped) without enterprise licensing
- You need **custom dialog flow control** (Lakera is detection, not orchestration)
- Your budget is **zero** (there is a free tier, but production scale requires paid plan)
- You need **MCP server or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Cloud API (2026-06)
- **Last update:** Active; Check Point integration ongoing
- **Enterprise:** Self-hosted option available via Check Point sales

## License

Content: CC BY 4.0  
Tool: Proprietary (Check Point)
