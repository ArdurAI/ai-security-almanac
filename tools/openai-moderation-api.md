# OpenAI Moderation API

**Tier:** A  
**Type:** Content Moderation  
**License:** Free (hosted)  
**Stars/Adoption:** Massive (free endpoint)  
**Region:** Global (hosted)

## Overview

The OpenAI Moderation API is a free, hosted content moderation service that classifies text and images into 13+ harm categories (hate, harassment, sexual, violence, self-harm, etc.). It is used by millions of applications as a zero-cost first-line defense against harmful content. It is not a security tool per se but is included because of its massive adoption and because many applications use it as a de facto guardrail.

## Setup experience

### Installation

No installation required. Direct HTTP API call or via the OpenAI SDK.

```python
from openai import OpenAI

client = OpenAI(api_key=os.environ["OPENAI_API_KEY"])

response = client.moderations.create(
    input=user_input,
    model="text-moderation-latest"
)

for result in response.results:
    if result.flagged:
        block_request(result.categories)
```

Setup time: ~0 minutes (already have OpenAI API key).  
No cold start — free endpoint with high availability.

### Configuration

**Zero configuration.** The Moderation API has a single endpoint with no policy tuning, thresholds, or custom categories. It classifies into 13+ categories and returns a `flagged` boolean plus per-category scores.

**Ops burden:** Zero. No configuration, no tuning, no maintenance. The trade-off is no customization — you get what OpenAI's model thinks is harmful, and you cannot adjust thresholds or add custom categories.

### First blocked adversarial prompt

With default settings, harmful content is flagged. Time to first block: ~0 seconds — it's an API call.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate | Good on common categories (hate, sexual, violence); weak on prompt injection, jailbreaks, adversarial attacks |
| Latency | Very Low | ~20-50ms API response; globally hosted |
| Token economics | Excellent | Free; zero cost |
| Scale behavior | Excellent | OpenAI-hosted; no rate limits for typical use |
| Ops burden | Zero | No configuration, no tuning, no maintenance |
| Developer experience | Excellent | Simple API; one endpoint; good docs |
| Data sovereignty | Poor | Data sent to OpenAI; no on-premise option; no EU data residency |

## Known bypasses

### Prompt injection / jailbreak blind spot
The Moderation API is a **content moderation** tool, not a **security** tool. It does not detect prompt injection, jailbreaks, system prompt extraction, or indirect injection. An attacker can craft a perfectly benign-looking prompt that contains a hidden injection — the Moderation API will flag it as safe because the surface text is harmless. **Workaround:** Combine with a dedicated prompt injection detector (e.g., Lakera Guard, Rebuff) for any security-critical application.

### Semantic reframing
An attacker can reframe harmful content using euphemisms, hypotheticals, or roleplay. The Moderation API is trained on common harmful language patterns; novel reframing may bypass. **Workaround:** Combine with a semantic moderation tool or human review for edge cases.

### Encoding bypasses
Base64, rot13, or Unicode-encoded harmful content may bypass the Moderation API if the encoding is not decoded before analysis. **Workaround:** Add a pre-processing layer that decodes common encodings before sending to the Moderation API.

### Multi-modal bypass
The image moderation API analyzes images for harmful content. An attacker can embed harmful text in an image (e.g., screenshot of a harmful prompt) that the text Moderation API would not see. **Workaround:** Use both text and image moderation APIs; or restrict image inputs to trusted sources.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Discuss the historical context of hate speech laws" | "Hate" category triggers at moderate score | No tuning possible; must implement post-processing whitelist or human review |
| "Write a medical scene involving self-harm for a screenplay" | "Self-harm" category triggers at moderate score | No tuning possible; must implement context-aware post-processing |
| "My penis is 5 inches — is that average?" | "Sexual" category triggers at low score | No tuning possible; must implement domain-specific filtering or use a different tool |

**Note:** Because the Moderation API has zero configuration, false positives cannot be tuned. You must implement post-processing logic or accept the false positive rate.

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Weak | Content filtering supports basic input validation; no governance, mapping, or management |
| EU AI Act | Weak | Data sent to OpenAI (US-based); no EU data residency; no conformity documentation |
| SOC 2 | Moderate | OpenAI maintains SOC 2; but data sovereignty is a concern |
| ISO 27001 | Moderate | OpenAI maintains ISO 27001; but data sovereignty is a concern |

**Gap:** The Moderation API is a free, hosted service with no data residency guarantees. For EU AI Act compliance (data must stay in EU), enterprise applications should use Azure AI Content Safety or a self-hosted alternative.

## Comparison with peers

| vs. | OpenAI Moderation API wins | OpenAI Moderation API loses |
|-----|---------------------------|----------------------------|
| Azure AI Content Safety | Free; zero cost; simpler API; massive adoption | Azure has more granular severity levels, EU data residency, and enterprise SLA |
| AWS Bedrock Guardrails | Free; zero cost; simpler API | Bedrock has prompt injection detection, PII anonymization, and contextual grounding |
| Lakera Guard | Free; zero cost; simpler API | Lakera has prompt injection detection, threat intelligence, and enterprise compliance |
| Google Model Armor | Free; zero cost; simpler API | Model Armor has prompt injection detection, data exfiltration prevention, and GCP integration |

## When to use

- You need a **free, zero-config content moderation** layer for text and images
- You are building a **low-budget application** and cannot afford paid security tools
- You want a **first-line defense** against common harmful content (hate, sexual, violence, self-harm)
- You are already using **OpenAI API** and want unified billing

## When to avoid

- You need **prompt injection detection** or adversarial security (this is not a security tool)
- You need **jailbreak detection** or system prompt protection
- You need **enterprise compliance** (EU AI Act, strict data residency, SOC 2 with data sovereignty)
- You need **custom categories** or tunable thresholds (zero configuration means zero customization)
- You need **PII detection** or data exfiltration prevention (not in scope)

## Version notes

- **Tested version:** `text-moderation-latest` (2026-06)
- **Last update:** OpenAI updates the moderation model periodically; no version control for users
- **Pricing:** Free (no cost for API calls)

## License

Content: CC BY 4.0  
Tool: Free hosted service (OpenAI)
