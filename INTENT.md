# Project Intent & Philosophy

## Why this almanac exists

The AI security landscape is exploding. Every week, a new guardrail framework, prompt injection detector, or AI safety platform launches, claiming to block attacks, reduce hallucinations, or ensure compliance. But **nobody independently verifies these claims**. The benchmarks are self-reported, the comparisons are marketing, and the "best tool" lists are driven by venture funding and affiliate SEO.

This almanac is the **public record of independent verification** for AI security, guardrails, and safety tools. It exists because security engineers and platform engineers need a single source of truth that answers:

- Does this guardrail actually block prompt injection at production scale?
- What's the real false positive rate on legitimate business prompts?
- How much latency does this security layer add per request?
- How does it fail when attackers use novel encoding, multi-turn jailbreaks, or indirect injection?
- What's the total cost of ownership per protected request?
- Can I trust the vendor's detection rate numbers?
- Does this tool meet my compliance requirements (NIST AI RMF, EU AI Act, SOC 2)?

## Core principles

### 1. Frozen methodology before results

The harness, judge model, attack prompts, detection thresholds, and scoring rubric are **fixed and published before any tool is tested**. This prevents "cherry-picking" the test cases that favor a particular vendor. If a tool doesn't fit the harness, we adapt the adapter — not the rules.

### 2. Ops-first evaluation

Most security benchmarks measure detection rate on static datasets. We measure **what a security engineer actually lives with**:
- Time from `pip install` to first blocked adversarial prompt
- False positive rate on real internal prompts (customer service, HR, legal, healthcare)
- Time to tune thresholds when a tool blocks legitimate work
- Upgrade pain when version N → N+1 breaks policy configurations
- Cost predictability at scale (cost per protected request, per million requests)
- Compliance burden: which standards are supported, how they map, and how long audit trails are retained

### 3. Raw data is always published

Every benchmark run produces a JSON file with every attack, every legitimate prompt, every block/allow decision, every latency measurement, every confidence score. These raw files are published alongside the summary. If you disagree with a ranking, you can re-analyze the data yourself.

### 4. No tool is above criticism

Every tool on the roster has been through a smoke gate. Every tool has bypasses, false positives, and blind spots. We document them honestly. A vendor relationship or sponsorship does not influence rankings. The only way a tool improves its score is by actually improving.

### 5. Living document, not a static snapshot

The almanac is updated monthly. Tools enter and exit the roster. Scores change as tools improve or degrade. Attack patterns evolve; so do our benchmarks. The "founding edition" is a snapshot; the current edition is the truth.

## Design philosophy

### The two-bar test

Every security tool must clear two bars to justify its existence:
1. **Beat the naive baseline** on detection accuracy (block rate vs. false positive rate at a fixed threshold)
2. **Beat the full-capability baseline** on latency overhead, ops burden, and cost per protected request

If a tool can't do both, it has no reason to exist as security infrastructure. A tool that is 5% more accurate at blocking jailbreaks but adds 500ms of latency and flags 30% of legitimate prompts is not worth adopting in production.

### The seven dimensions

We score every tool on seven dimensions because no single number captures "good security infrastructure":

| Dimension | Why it matters | Security-specific meaning |
|-----------|---------------|---------------------------|
| **Accuracy** | Does it correctly distinguish attack from legitimate use? | Detection rate at a fixed false positive rate (e.g., 1% FPR); ROC-AUC equivalent |
| **Latency** | Does it respond fast enough for real-time use? | Guardrail overhead per request (p50, p95, p99); end-to-end latency impact |
| **Token economics** | Does it cost what you expect? | Cost per protected request, per 1M requests; cost per blocked attack |
| **Scale behavior** | What happens when you 10x the load? | Throughput with security checks enabled; false positive rate under burst load; queue saturation |
| **Ops burden** | How much of your life does it consume? | Time to configure policies; time to tune thresholds; policy drift management; upgrade pain |
| **Developer experience** | Is it pleasant or painful to use? | Policy language clarity; API ergonomics; error messages when a block fires; debugging a false positive |
| **Data sovereignty** | Can you run it yourself? Audit it? | On-premise / air-gapped deployment; audit log retention; compliance mapping (NIST AI RMF, EU AI Act, SOC 2); model/data residency |

### The adapter pattern

Every tool is tested through a **CategoryAdapter** — a frozen interface that the tool must satisfy. The adapter handles setup, policy configuration, benign querying, adversarial querying, and teardown. This means:
- Tools are tested identically (same attacks, same benign prompts, same thresholds)
- The adapter is the only thing that changes per tool
- New tools can be added without changing the harness
- The adapter is published and open for review

### The canary

Every benchmark batch starts with a **no-tool baseline** (the "canary"). If the benchmark leaked answers anywhere, or if the benign prompt set contained hidden attack patterns, the canary would score abnormally. The canary must score exactly as expected — this is a hard invariant. If it doesn't, the entire batch is invalid.

## Who this is for

- **Security engineers** evaluating which guardrail or safety tool to adopt
- **Platform engineers** integrating security into their AI stack
- **CISOs / Compliance officers** making build-vs-buy decisions with actual data and compliance mapping
- **AI safety researchers** studying the effectiveness of guardrails and jailbreak mitigations
- **Red-teamers** looking for bypass techniques and blind spots in existing tools
- **Open-source maintainers** who want independent benchmarking of their security project
- **Vendors** who want to improve their tools based on real evidence, not self-reported benchmarks

## What this is NOT

- Not a marketing site for any security vendor
- Not a "best of" list based on GitHub stars or funding rounds
- Not a tutorial on how to use any security tool
- Not a replacement for your own red-team exercises and penetration testing
- Not a static document that never changes (attack patterns evolve; so do we)
- Not a compliance certification (we map to standards; we do not certify)

## The "Quest"

The "Security Engineer's Quest for the Best" is the ongoing effort to test, measure, and rank every security tool on the roster. It's not a one-time effort. It's a continuous process of:
1. **Discovery** — finding new tools via research, community, and submissions
2. **Triage** — deciding if a tool is serious enough to enter the roster
3. **Smoke gate** — running every tool through an identical 3-turn scenario: configure a basic policy, send a benign prompt, send an adversarial prompt and verify it is blocked
4. **Benchmark** — running standard suites (OWASP, Gandalf) + custom adversarial tests + stress tests + compliance mapping
5. **Publication** — publishing raw data + summary + per-tool deep-dives (including bypass techniques and false positive examples)
6. **Iteration** — re-testing as tools update, as new attack patterns emerge, as new compliance standards are published

## How to challenge a result

If you believe a ranking or score is wrong:
1. Check the **raw results JSON** — the data is public
2. Check the **adapter implementation** — the adapter code is public
3. Check the **attack prompts and judge prompts** — they are frozen and public
4. File an issue with a specific claim and evidence (e.g., "Tool X blocks 95% on this prompt set, but the almanac reports 60% — here's a reproduction")
5. We'll re-run the test or update the methodology if warranted

## Governance

- **ArdurAI** maintains the almanac and runs the Quest
- **Methodology changes** require a public RFC and at least one edition cycle of notice (e.g., adding a new adversarial benchmark, changing the FPR target from 1% to 0.1%)
- **Tool additions/removals** are decided by the triage criteria (stars, last push, community activity, seriousness)
- **Benchmark results** are machine-generated; summaries are human-reviewed for fairness and security context
- **Conflicts of interest** are disclosed (e.g., ArdurAI contributes to some tools on the roster); mitigation is identical harness for all

## License

Content: CC BY 4.0  
Harness code: MIT  
Raw data: CC BY 4.0
