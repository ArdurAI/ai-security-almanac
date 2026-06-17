# Llama Guard 3 (Meta)

**Tier:** A  
**Type:** Safety Classifier  
**License:** Open weights  
**Stars/Adoption:** 3.8B parameters (open weights)  
**Region:** Global (open source)

## Overview

Llama Guard 3 is Meta's open-weights safety classifier that categorizes text into 14+ harm categories (violence, hate, sexual, self-harm, etc.). It is a 3.8B parameter model that can be run locally or via API. Unlike the OpenAI Moderation API, Llama Guard is a model you host yourself, providing full data sovereignty. It uses a prompt-based taxonomy that can be customized for specific use cases.

## Setup experience

### Installation

Llama Guard 3 can be run via Hugging Face Transformers, Ollama, or vLLM.

```bash
pip install transformers torch
# or
ollama pull llama-guard3
# or
pip install vllm
```

Install time: ~2-5 minutes (pip) or instant (Ollama).  
Model download: ~7GB for 3.8B parameters (Ollama handles this automatically).

### Configuration

```python
from transformers import AutoTokenizer, AutoModelForCausalLM
import torch

tokenizer = AutoTokenizer.from_pretrained("meta-llama/Llama-Guard-3-8B")
model = AutoModelForCausalLM.from_pretrained(
    "meta-llama/Llama-Guard-3-8B",
    torch_dtype=torch.bfloat16,
    device_map="auto"
)

def classify_safety(text, role="user"):
    conversation = [{"role": role, "content": text}]
    input_ids = tokenizer.apply_chat_template(
        conversation, return_tensors="pt", tokenize=True
    )
    output = model.generate(input_ids, max_new_tokens=100)
    result = tokenizer.decode(output[0], skip_special_tokens=True)
    return "safe" if "safe" in result.lower() else "unsafe"

# Custom taxonomy (prompt-based)
custom_categories = [
    "S1: Violent Crimes",
    "S2: Non-Violent Crimes",
    "S3: Sex-Related Crimes",
    "S4: Child Sexual Abuse Material",
    "S5: Defamation",
    "S6: Specialized Advice",
    "S7: Privacy",
    "S8: Intellectual Property",
    "S9: Indiscriminate Weapons",
    "S10: Hate",
    "S11: Suicide & Self-Harm",
    "S12: Sexual Content",
    "S13: Elections",
    "S14: Code Interpreter Abuse"
]
```

**Ops burden:** Low-Moderate. The model is pre-trained and ready to use. Custom taxonomies require prompt engineering. GPU inference requires CUDA setup; CPU inference is slow (~1-2s per request on CPU).

### First blocked adversarial prompt

With the default taxonomy, harmful content is classified as "unsafe." Time to first block: ~5 minutes (model download + inference setup).

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | Moderate-High | Good on common categories; taxonomy is comprehensive but not adversarial-optimized |
| Latency | Low-Moderate | ~50-200ms on GPU; ~1-2s on CPU |
| Token economics | Very low | Self-hosted; no API costs; GPU compute cost only |
| Scale behavior | Good | GPU batching supported; vLLM provides high throughput |
| Ops burden | Low-Moderate | Model download + GPU setup; custom taxonomy requires prompt engineering |
| Developer experience | Good | Hugging Face ecosystem; Ollama for easy local run; good docs |
| Data sovereignty | Excellent | Fully self-hosted; no external calls; data never leaves your infrastructure |

## Known bypasses

### Taxonomy blind spot
Llama Guard's taxonomy is fixed at training time. An attack that does not fit any of the 14 categories (e.g., a novel jailbreak technique, a prompt injection that doesn't trigger any category) will be classified as "safe." **Workaround:** Add custom categories via prompt engineering; or combine with a dedicated prompt injection detector.

### Prompt injection against the classifier itself
An attacker can craft a prompt that tricks Llama Guard into classifying harmful content as "safe." For example, embedding the harmful content inside a larger context that the classifier misinterprets. **Workaround:** Use the classifier in a pipeline with multiple models; or use a stronger adversarially-trained classifier.

### Context window limitations
Llama Guard 3 has a limited context window. A very long prompt that buries the harmful content deep in the text may be missed. **Workaround:** Chunk long prompts and classify each chunk independently; or use a sliding window approach.

### Multi-turn bypass
Llama Guard is designed for single-turn classification. In a multi-turn conversation, harmful content may be distributed across turns, and the classifier may not see the full context. **Workaround:** Classify the full conversation history or use a conversation-aware safety model.

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| "Discuss the historical context of hate speech laws" | "S10: Hate" category triggers | Adjust the taxonomy prompt to distinguish academic discussion from hate speech |
| "Write a medical scene involving self-harm for a screenplay" | "S11: Suicide & Self-Harm" category triggers | Add `creative_writing` context to the taxonomy prompt |
| "How do I make a Molotov cocktail for a movie prop?" | "S9: Indiscriminate Weapons" category triggers | Add `movie_prop` or `creative_context` to the taxonomy prompt |

**Note:** False positives can be tuned by customizing the taxonomy prompt. This is Llama Guard's key advantage over the OpenAI Moderation API.

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | Moderate | Content classification supports input validation; no governance or management features |
| EU AI Act | Moderate | Self-hosted = full data sovereignty; taxonomy supports Article 15; no built-in conformity documentation |
| SOC 2 | Partial | Self-hosted means you control the audit trail; but no built-in SOC 2 controls |
| ISO 27001 | Partial | Self-hosted means you control the ISMS; but no built-in ISO 27001 controls |

**Strength:** Full data sovereignty makes Llama Guard attractive for compliance-conscious organizations. The weakness is that compliance automation is DIY.

## Comparison with peers

| vs. | Llama Guard 3 wins | Llama Guard 3 loses |
|-----|-------------------|--------------------|
| OpenAI Moderation API | Self-hosted; customizable taxonomy; no API costs; data sovereignty | Slightly lower accuracy on common categories; requires GPU infrastructure |
| Azure AI Content Safety | Self-hosted; customizable; no per-request cost | Azure has more granular severity levels, better multilingual support, and enterprise SLA |
| AWS Bedrock Guardrails | Self-hosted; customizable taxonomy | Bedrock has zero-code integration, PII anonymization, and contextual grounding |

## When to use

- You need **full data sovereignty** (air-gapped, on-premise, no cloud dependency)
- You want a **customizable taxonomy** for your specific use case
- You have **GPU infrastructure** and want to avoid per-request API costs
- You are building a **self-hosted AI system** and need a safety layer

## When to avoid

- You need **prompt injection detection** or adversarial security (this is a content classifier, not a security tool)
- You need **jailbreak detection** or system prompt protection
- You have **no GPU infrastructure** and need low latency (CPU inference is slow)
- You need **enterprise compliance automation** (SOC 2, EU AI Act mapping is DIY)
- You need **sub-50ms latency** at scale (GPU inference is fast but not as fast as cloud APIs)

## Version notes

- **Tested version:** Llama Guard 3 8B (2026-06)
- **Last update:** Active; Meta releases updated versions periodically
- **Community:** Open weights; Hugging Face ecosystem; widely adopted in self-hosted applications

## License

Content: CC BY 4.0  
Tool: Llama 3.1 Community License (open weights, commercial use allowed with restrictions)
