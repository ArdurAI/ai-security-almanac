# Tool Name

**Tier:** A|B|C  
**Type:** <Guardrail Framework | Prompt Injection Detector | PII Scrubber | Output Filter | Safety Aligner | Policy Engine | Content Moderator | Adversarial Tester>  
**License:** <License>  
**GitHub / URL:** <link>  
**Stars / Adoption:** <stars or adoption metric>  
**Region:** <Region>

## Overview

<2-3 sentence description of what the tool does, its key differentiator, and why it matters.>

## Setup experience

### Installation

```bash
<installation commands>
```

Install time: <X seconds/minutes>.  
Cold start: <any first-run overhead>.

### Configuration

```python
<minimal configuration example>
```

**Ops burden:** <Low / Moderate / High>. <Explanation of configuration complexity, learning curve, and ongoing maintenance.>

### First blocked adversarial prompt

<Description of the smoke gate result — how long from install to first block, and what was blocked.>

## Benchmark results

> *Benchmarks pending. Results will be published in `benchmarks/` when the harness run completes.*

### Expected profile (based on smoke gate)

| Dimension | Projection | Notes |
|-----------|-----------|-------|
| Accuracy | <High / Moderate / Low> | <Reasoning> |
| Latency | <Very Low / Low / Moderate / High / Very High> | <Reasoning> |
| Token economics | <Very Low / Low / Moderate / High / Very High> | <Reasoning> |
| Scale behavior | <Excellent / Good / Moderate / Poor> | <Reasoning> |
| Ops burden | <Very Low / Low / Moderate / High / Very High> | <Reasoning> |
| Developer experience | <Excellent / Good / Moderate / Poor> | <Reasoning> |
| Data sovereignty | <Excellent / Good / Moderate / Poor> | <Reasoning> |

## Known bypasses

### Bypass name 1
<Description of the bypass technique and how it works.>

**Workaround:** <How to mitigate or avoid this bypass.>

### Bypass name 2
<Description of the bypass technique and how it works.>

**Workaround:** <How to mitigate or avoid this bypass.>

## False positive examples

| Benign prompt | Why it was flagged | How to tune |
|--------------|-------------------|-------------|
| `"<prompt>"` | `<explanation>` | `<tuning recommendation>` |
| `"<prompt>"` | `<explanation>` | `<tuning recommendation>` |

## Compliance mapping

| Standard | Supported | Notes |
|----------|-----------|-------|
| NIST AI RMF | <Strong / Partial / Weak / None> | <Notes> |
| EU AI Act | <Strong / Partial / Weak / None> | <Notes> |
| SOC 2 | <Strong / Partial / Weak / None> | <Notes> |
| ISO 27001 | <Strong / Partial / Weak / None> | <Notes> |

**Gap:** <Any significant compliance gaps.>

## Comparison with peers

| vs. | This tool wins | This tool loses |
|-----|---------------|-----------------|
| Peer A | <Advantage> | <Disadvantage> |
| Peer B | <Advantage> | <Disadvantage> |

## When to use

- <Use case 1>
- <Use case 2>
- <Use case 3>

## When to avoid

- <Avoid case 1>
- <Avoid case 2>
- <Avoid case 3>

## Version notes

- **Tested version:** <version>
- **Last push:** <activity status>
- **Community:** <stars, contributors, activity level>

## License

Content: CC BY 4.0  
Tool: <License>
