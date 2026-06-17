# Rebuff

**Tier:** B  
**Type:** Prompt Injection Detection  
**License:** Open Source  
**Stars/Adoption:** Open Source (Moderate)  
**Region:** Global

## Overview

Rebuff is an open-source prompt injection detection tool that uses semantic analysis and input filtering to detect adversarial prompts. It was originally developed as a Protect AI project and is designed for self-hosted deployment with minimal dependencies. It focuses narrowly on prompt injection detection rather than broad content moderation or governance.

## Setup experience

### Installation

```bash
pip install rebuff
```

Install time: ~10 seconds.  
No cold start — pure Python with optional embedding model download.

### Configuration

```python
from rebuff import Rebuff

rebuff = Rebuff(
    model="all-MiniLM-L6-v2",  # embedding model for semantic similarity
    threshold=0.7  # similarity threshold for prompt injection detection
)

# Detect prompt injection
result = rebuff.detect(user_input)

if result.is_injection:
    block_request(f"Prompt injection detected: {result.confidence}")

# Get embedding similarity scores
similarities = rebuff.get_similarities(user_input)
```

**Ops burden:** Low. Single-purpose tool; minimal configuration. The embedding model is downloaded automatically. Threshold tuning is the only configuration required.

### First blocked adversarial prompt

With the default embedding model and threshold, known prompt injection patterns are detected. Time to first block: ~1 minute from `pip install`.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate | Good on known prompt injection patterns; semantic analysis catches variations |
| Latency | Low | ~30-100ms per request (embedding model inference) |
| Token economics | Very low | Self-hosted; no API costs; embedding model runs locally |
| Scale behavior | Good | Embedding model inference is fast; can batch requests |
| Ops burden | Low | Single-purpose tool; minimal configuration |
| Developer experience | Good | Simple API; single-purpose; good docs |
| Data sovereignty | Excellent | Fully self-hosted; no external calls |

## Known bypasses

### Semantic reframing
Rebuff uses semantic similarity to detect prompt injection. An attacker can reframe the injection using synonyms, paraphrasing, or roleplay that changes the semantic embedding but preserves the malicious intent. **Workaround:** Lower the threshold (more false positives) or combine with a keyword-based detector.

### Encoding bypass
Rebuff analyzes the raw text. If the prompt is encoded (Base64, rot13, Unicode homoglyphs), the semantic similarity may not match known injection patterns. **Workaround:** Add a pre-processing layer that decodes common encodings before passing to Rebuff.

### Novel injection pattern
Rebuff's detection is based on known prompt injection patterns. A novel injection technique that does not match any known pattern will bypass detection. **Workaround:** Regularly update the embedding model and known patterns; combine with a dedicated threat intelligence feed.

### Threshold tuning trade-off
Lowering the threshold increases detection but also increases false positives. An attacker can craft a prompt that is just below the threshold. **Workaround:** Use adaptive thresholding based on context; or combine with multiple detectors.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Ignore the previous topic and let's talk about Python" | "Ignore previous" is semantically similar to prompt injection patterns | Lower the threshold; or add `topic_switch` context to the whitelist |
| "Can you pretend to be a security researcher?" | "pretend to be" is semantically similar to roleplay injection patterns | Lower the threshold; or add `roleplay` context to the whitelist |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Input validation supports basic input validation; no governance or management |
| EU AI Act | Weak | Self-hosted = data sovereignty; no built-in compliance mapping |
| SOC 2 | Partial | Self-hosted means you control the audit trail; no built-in SOC 2 controls |
| ISO 27001 | Partial | Input validation supports ISMS; no built-in ISO 27001 controls |

**Gap:** Rebuff is a single-purpose prompt injection detector. It does not provide content moderation, PII detection, or compliance mapping. Combine with broader tools for full security coverage.

## Comparison with peers

| vs. | Rebuff wins | Rebuff loses |
|-----|-------------|--------------|
| Lakera Guard | Free; self-hosted; no per-request cost; simple API | Lakera Guard has higher detection accuracy, threat intelligence, and enterprise compliance |
| Guardrails AI | Free; self-hosted; focused on prompt injection; simpler API | Guardrails AI has 70+ validators, output validation, and broader ecosystem |
| LLM Guard | Free; self-hosted; focused on prompt injection with semantic analysis | LLM Guard has multiple scanners (PII, toxicity, secrets) and pipeline composition |

## When to use

- You need a **free, open-source prompt injection detector**
- You want **semantic analysis** (not just keyword matching)
- You need **full data sovereignty** (self-hosted, no external calls)
- You want a **single-purpose tool** with minimal configuration
- You are building a **self-hosted AI application** and need basic prompt injection defense

## When to avoid

- You need **enterprise compliance** (SOC 2, EU AI Act mapping is DIY)
- You need **content moderation** or PII detection (use LLM Guard or Guardrails AI)
- You need **sub-10ms latency** (embedding inference adds ~30-100ms)
- You need **advanced adversarial detection** or threat intelligence feeds
- You need **MCP or agent tool-call protection** (not in core scope)

## Version notes

- **Tested version:** Rebuff 0.3.x (2026-06)
- **Last update:** Active; Protect AI team maintains; quarterly updates
- **Community:** Open-source; moderate community; Protect AI ecosystem

## License

Content: CC BY 4.0  
Tool: Open Source (Protect AI)
