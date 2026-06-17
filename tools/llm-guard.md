# LLM Guard (Protect AI)

**Tier:** B  
**Type:** Input/Output Scanners  
**License:** Open Source  
**Stars/Adoption:** Open Source (Growing)  
**Region:** Global

## Overview

LLM Guard is an open-source input/output scanner framework developed by Protect AI (now part of Palo Alto Networks). It provides multiple scanners for prompt injection, PII, toxic content, secrets, and other security checks. It is designed as a pipeline of scanners that can be composed and customized for specific use cases. Being open-source and from the Protect AI team, it benefits from the same threat intelligence but is a lighter-weight, standalone tool.

## Setup experience

### Installation

```bash
pip install llm-guard
```

Install time: ~20 seconds.  
No cold start — pure Python with optional ML model downloads.

### Configuration

```python
from llm_guard import LLMGuard, Scanner

guard = LLMGuard(scanners=[
    Scanner.prompt_injection(),
    Scanner.pii(),
    Scanner.toxicity(),
    Scanner.secrets(),
    Scanner.invisible_text(),  # zero-width characters, homoglyphs
    Scanner.ban_topics(topics=["violence", "hate", "sexual"]),
])

# Input scanning
input_result = guard.scan_input(user_input)
if input_result.is_blocked:
    block_request(input_result.reason)

# Output scanning
output_result = guard.scan_output(llm_response)
if output_result.is_blocked:
    block_request(output_result.reason)
```

**Ops burden:** Low. Pre-configured scanners; pipeline composition is straightforward. Custom scanners can be written by subclassing the `Scanner` class. No GPU required for most scanners.

### First blocked adversarial prompt

With the default scanner pipeline, prompt injection and PII are detected. Time to first block: ~2 minutes from `pip install`.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | Good on common scanners; inherits Protect AI threat intelligence |
| Latency | Low-Moderate | ~50-200ms per scanner; pipeline overhead stacks linearly |
| Token economics | Very low | Self-hosted; no API costs |
| Scale behavior | Good | Python-native; async support; scanner pipeline can be parallelized |
| Ops burden | Low | Pre-configured scanners; pipeline composition is straightforward |
| Developer experience | Good | Simple API; good docs; custom scanner support |
| Data sovereignty | Excellent | Fully self-hosted; no external calls |

## Known bypasses

### Scanner ordering bypass
The order of scanners in the pipeline matters. An attacker can craft a prompt that bypasses the first scanner but is caught by the second — or vice versa. If the pipeline is misconfigured, the attack slips through. **Workaround:** Use a comprehensive scanner pipeline; test scanner ordering with red team exercises.

### Custom scanner bypass
If a custom scanner is poorly written, it may have false negatives or false positives. An attacker can exploit the custom scanner's weaknesses. **Workaround:** Red-team custom scanners before deployment; use the pre-built scanners as a baseline.

### Encoding bypass
LLM Guard's `invisible_text` scanner detects zero-width characters and homoglyphs. However, novel encoding schemes or polyglot payloads may bypass. **Workaround:** Add a custom scanner for novel encoding schemes; or use a pre-processing layer that normalizes text.

### Pipeline resource exhaustion
A sustained attack with many scanners can exhaust CPU resources, causing latency spikes or dropped requests. **Workaround:** Size infrastructure for attack load; use async pipeline execution; or disable non-essential scanners under load.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "My password is Password123 — can you help me create a stronger one?" | Secrets scanner flags `Password123` as a credential | Add `password_generation` context or tune the secrets scanner |
| "Write a Python script that downloads a file from https://example.com" | Secrets scanner flags the URL as potentially malicious | Add `developer_tools` context or whitelist known domains |
| "[Zero-width character in the middle of a word]" | Invisible text scanner flags zero-width characters | Add `allowed_invisible_chars` configuration or whitelist specific Unicode ranges |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | Input/output scanners support input validation and output filtering |
| EU AI Act | Moderate | Self-hosted = data sovereignty; PII + toxicity scanners support Article 15 |
| SOC 2 | Partial | Self-hosted means you control the audit trail; no built-in SOC 2 controls |
| ISO 27001 | Partial | Input/output validation supports ISMS; no built-in ISO 27001 controls |

**Strength:** LLM Guard is a free, open-source scanner framework with Protect AI's threat intelligence. It is ideal for self-hosted applications that need basic input/output security without enterprise licensing.

## Comparison with peers

| vs. | LLM Guard wins | LLM Guard loses |
|-----|---------------|-----------------|
| Guardrails AI | Free; lightweight; Protect AI threat intelligence | Guardrails AI has 70+ Hub validators and broader ecosystem |
| Rebuff | Multiple scanners (prompt injection, PII, toxicity, secrets); pipeline composition | Rebuff has more focused prompt injection detection with semantic analysis |
| Lakera Guard | Free; self-hosted; no per-request cost | Lakera Guard has higher detection accuracy and threat intelligence |

## When to use

- You need a **free, open-source input/output scanner** framework
- You want **multiple scanners** (prompt injection, PII, toxicity, secrets) in a pipeline
- You need **custom scanner** support for domain-specific checks
- You want **full data sovereignty** (self-hosted, no external calls)
- You are already using **Protect AI / Prisma AIRS** and want a lightweight scanner

## When to avoid

- You need **enterprise compliance** (SOC 2, EU AI Act mapping is DIY)
- You need **sub-10ms latency** (scanner pipeline overhead stacks)
- You need **advanced adversarial detection** or threat intelligence feeds
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** LLM Guard 0.5.x (2026-06)
- **Last update:** Active; Protect AI team maintains; quarterly scanner updates
- **Community:** Open-source; growing community; Protect AI ecosystem

## License

Content: CC BY 4.0  
Tool: Open Source (Protect AI)
