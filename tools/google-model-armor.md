# Google Model Armor

**Tier:** A  
**Type:** Input/Output Filtering  
**License:** Proprietary  
**Stars/Adoption:** GCP-native (Enterprise)  
**Region:** Global (GCP regions)

## Overview

Google Model Armor is an input/output filtering service for Google Cloud's Vertex AI and Gemini models. It detects prompt injection, data leakage, and harmful content. It is integrated into the Vertex AI platform and can be applied to any model deployed through Vertex AI.

## Setup experience

### Installation

Google Model Armor is a **managed service** — no installation required. It is configured via the Google Cloud Console or gcloud CLI.

```bash
gcloud model-armor templates create production-template \
  --filter-config-file=filter-config.json \
  --rai-settings-file=rai-settings.json
```

Setup time: ~5 minutes (console) or ~2 minutes (CLI with pre-written configs).  
No cold start — Model Armor is a regional GCP service.

### Configuration

```json
{
  "filterConfig": {
    "promptInjectionFilter": {
      "enabled": true,
      "confidenceThreshold": 0.8
    },
    "dataExfiltrationFilter": {
      "enabled": true,
      "sensitiveDataTypes": ["CREDIT_CARD", "SSN", "EMAIL"]
    },
    "maliciousUriFilter": {
      "enabled": true
    }
  },
  "raiSettings": {
    "hateSpeech": {"enabled": true, "threshold": "BLOCK_LOW_AND_ABOVE"},
    "sexuallyExplicit": {"enabled": true, "threshold": "BLOCK_MEDIUM_AND_ABOVE"},
    "dangerousContent": {"enabled": true, "threshold": "BLOCK_LOW_AND_ABOVE"},
    "harassment": {"enabled": true, "threshold": "BLOCK_MEDIUM_AND_ABOVE"}
  }
}
```

**Ops burden:** Very low. GCP Console provides a visual template builder. Templates are applied at the Vertex AI API layer. No code changes required for Vertex AI models.

### First blocked adversarial prompt

With the prompt injection filter enabled, known jailbreaks and prompt injection attempts are blocked. Time to first block: ~0 minutes — it's applied at the API layer automatically once configured.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | Google's safety models; prompt injection filter is improving but not cutting-edge |
| Latency | Very Low | Native Vertex AI integration; ~5-15ms overhead |
| Token economics | Low-Moderate | Per-request pricing; scales with Vertex AI usage |
| Scale behavior | Excellent | GCP-native; auto-scales with Vertex AI |
| Ops burden | Very Low | Console-driven; no code changes; template-based |
| Developer experience | Excellent | Zero-code integration for Vertex AI; familiar GCP Console |
| Data sovereignty | Good | GCP regions; data stays in chosen region |

## Known bypasses

### Prompt injection semantic bypass
Google's prompt injection filter uses semantic detection. An attacker can craft a prompt that does not contain obvious injection keywords but uses social engineering, roleplay, or indirect persuasion. **Workaround:** Combine with a dedicated prompt injection detector (e.g., Lakera Guard) for high-security applications.

### Data exfiltration via encoding
Model Armor's data exfiltration filter detects common PII types (credit card, SSN, email). An attacker can encode the data (Base64, rot13) before exfiltration, bypassing the filter. **Workaround:** Add a pre-processing layer that decodes common encodings before passing to Model Armor.

### Malicious URI evasion
The malicious URI filter detects known phishing and malware URLs. An attacker can use URL shorteners, redirects, or newly registered domains that are not yet in Google's threat database. **Workaround:** Add a secondary URL reputation service or restrict all external URLs.

### Non-Vertex AI bypass
Model Armor only works with Vertex AI API calls. If the application bypasses Vertex AI and calls the LLM directly (e.g., direct OpenAI API call), Model Armor is not applied. **Workaround:** Enforce Vertex AI usage via IAM policies and network controls.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "My email is john@example.com for the newsletter" | Data exfiltration filter flags the email address | Lower the email detection threshold or add `newsletter_signup` context |
| "Write a Python script that downloads a file from https://example.com" | Malicious URI filter flags the URL | Add `developer_tools` context or whitelist known domains |
| "Can you explain how prompt injection works?" | Prompt injection filter flags the phrase "prompt injection" | Add `security_education` context or adjust confidence threshold |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | GCP provides NIST mapping guides; Model Armor supports input validation and output filtering |
| EU AI Act | Moderate | Data residency in EU regions; content filtering supports Article 15; no built-in conformity documentation |
| SOC 2 | Strong | Inherits GCP SOC 2 Type II |
| ISO 27001 | Strong | Inherits GCP ISO 27001 certification |

**Strength:** Google Cloud's compliance infrastructure is enterprise-grade. The weakness is that Model Armor is a generic filter, not a specialized security tool — it lacks advanced adversarial detection, threat intelligence, and red-team capabilities.

## Comparison with peers

| vs. | Google Model Armor wins | Google Model Armor loses |
|-----|------------------------|--------------------------|
| AWS Bedrock Guardrails | Prompt injection detection is stronger than Bedrock's; Vertex AI integration is deep | Bedrock has more granular PII anonymization and contextual grounding |
| Azure AI Content Safety | Prompt injection detection; data exfiltration filter; URI filter | Azure has more granular severity levels and better multilingual support |
| Lakera Guard | Zero setup for GCP users; no per-request cost beyond Vertex AI | Lakera has higher detection accuracy and threat intelligence |

## When to use

- You are already using **Google Vertex AI** or **Gemini** for LLM inference
- You want **zero-code guardrails** applied at the API layer
- You need **prompt injection detection, data exfiltration prevention, and content filtering** in one service
- You want **GCP-native compliance** (SOC 2, ISO 27001) with minimal effort

## When to avoid

- You are using **non-Vertex AI LLM providers** (OpenAI, Anthropic direct, Azure, etc.)
- You need **cutting-edge prompt injection detection** (Model Armor's filter is good but not state-of-the-art)
- You need **custom adversarial detection** or threat intelligence feeds
- You need **response-side hallucination scoring** (not in scope)

## Version notes

- **Tested version:** Model Armor (2026-06)
- **Last update:** Active; Google releases updates quarterly
- **Pricing:** Per-request pricing; included in Vertex AI pricing or separate per-request fees

## License

Content: CC BY 4.0  
Tool: Proprietary (Google Cloud)
