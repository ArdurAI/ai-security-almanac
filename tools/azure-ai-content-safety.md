# Azure AI Content Safety

**Tier:** A  
**Type:** Content Moderation  
**License:** Proprietary  
**Stars/Adoption:** Enterprise (Azure-native)  
**Region:** Global (Azure regions)

## Overview

Azure AI Content Safety is Microsoft's content moderation service for detecting harmful content in text and images. It classifies content into categories (hate, sexual, violence, self-harm) with severity levels and supports multilingual detection. It is part of the broader Azure AI services ecosystem.

## Setup experience

### Installation

Azure AI Content Safety is a **managed API service** — no installation required.

```bash
pip install azure-ai-contentsafety
```

Install time: ~10 seconds.  
API key setup: Create an Azure AI Content Safety resource in the Azure Portal.

### Configuration

```python
from azure.ai.contentsafety import ContentSafetyClient
from azure.core.credentials import AzureKeyCredential

client = ContentSafetyClient(
    endpoint="https://<resource-name>.cognitiveservices.azure.com/",
    credential=AzureKeyCredential(os.environ["AZURE_CONTENT_SAFETY_KEY"])
)

# Analyze text
response = client.analyze_text(
    body={"text": user_input},
    categories=["Hate", "Sexual", "Violence", "SelfHarm"],
    severity=True
)

for result in response.categories_analysis:
    if result.severity > 2:  # 0-4 severity scale
        block_request(result.category)
```

**Ops burden:** Very low. Pre-trained models for common categories; no custom training required. Severity levels (0-4) provide granular control. Multilingual support is automatic.

### First blocked adversarial prompt

With default settings, harmful content (hate, sexual, violence, self-harm) is flagged. Time to first block: ~1 minute from API key setup.

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | Strong on standard content categories; weaker on prompt injection specifically |
| Latency | Very Low | ~10-20ms API response |
| Token economics | Low-Moderate | Per-request pricing; competitive with AWS/Google |
| Scale behavior | Excellent | Azure-native; auto-scaling |
| Ops burden | Very Low | Pre-trained categories; severity levels; multilingual |
| Developer experience | Excellent | Simple REST API; SDK for Python/JS/C#; good docs |
| Data sovereignty | Good | Azure regions; data stays in chosen region |

## Known bypasses

### Semantic reframing
An attacker can reframe harmful content using euphemisms, hypotheticals, or roleplay. For example, "I hate [group]" is blocked, but "In a fictional story, a character expresses prejudice against [group]" may bypass. **Workaround:** Lower severity threshold or add custom categories for edge cases.

### Code injection as formatting
An attacker can embed malicious code in what appears to be benign formatting instructions (e.g., markdown, HTML). Azure AI Content Safety focuses on toxicity categories, not code safety. **Workaround:** Combine with a code-specific scanner or input sanitization layer.

### Multilingual bypass
While Azure supports multilingual detection, low-resource languages or dialects may have weaker detection. An attacker can craft attacks in under-resourced languages. **Workaround:** Combine with language-specific moderation or restrict supported languages.

### Prompt injection blind spot
Azure AI Content Safety is a **content moderation** tool, not a **prompt injection detector**. It does not detect jailbreaks, system prompt extraction attempts, or indirect injection. **Workaround:** Combine with a dedicated prompt injection detector (e.g., Lakera Guard, Rebuff) for security use cases.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Discuss the historical context of hate speech laws" | "Hate" + "speech" triggers Hate category at severity 2 | Increase severity threshold to 3; or add academic context |
| "Write a medical scene involving self-harm for a screenplay" | "SelfHarm" category triggers at severity 3 | Add `creative_writing` context or whitelist screenplay domain |
| "Translate this violent video game description to Japanese" | "Violence" category triggers at severity 2 | Add `video_games` or `translation` context |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | Content filtering supports Measure; no built-in Govern/Manage |
| EU AI Act | Moderate | Data residency in EU regions; content filtering supports Article 15; no conformity documentation |
| SOC 2 | Strong | Inherits Azure SOC 2 Type II |
| ISO 27001 | Strong | Inherits Azure ISO 27001 certification |

**Gap:** Azure AI Content Safety is a general content moderation tool, not a specialized AI security tool. It does not map to adversarial security controls or red-team requirements.

## Comparison with peers

| vs. | Azure AI Content Safety wins | Azure AI Content Safety loses |
|-----|-----------------------------|-------------------------------|
| AWS Bedrock Guardrails | More granular severity levels (0-4); better multilingual support; standalone API (not Bedrock-only) | Bedrock has contextual grounding and PII anonymization |
| Google Model Armor | Azure has broader category coverage and maturity | Model Armor has stronger prompt injection detection and Vertex AI integration |
| OpenAI Moderation API | Enterprise SLA; Azure ecosystem; severity levels | OpenAI Moderation API is free and has more harm categories (13+) |

## When to use

- You need **content moderation** (toxicity, hate, sexual, violence, self-harm) for text and images
- You are already in the **Azure ecosystem** and want unified billing, IAM, and compliance
- You need **multilingual content moderation** out of the box
- You need **severity levels** (0-4) for granular control

## When to avoid

- You need **prompt injection detection** (this is a content moderation tool, not a security firewall)
- You need **jailbreak detection** or adversarial defense
- You need **PII detection or data exfiltration prevention** (use Azure AI Language PII or a dedicated tool)
- You need **response-side hallucination scoring** (not in scope)

## Version notes

- **Tested version:** Azure AI Content Safety API v1.0 (2026-06)
- **Last update:** Active; Microsoft updates models quarterly
- **Pricing:** Per 1K text records; ~$1.00 per 1K records; image moderation per 1K images

## License

Content: CC BY 4.0  
Tool: Proprietary (Microsoft)
