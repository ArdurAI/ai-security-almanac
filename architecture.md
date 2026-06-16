# Architecture: Security, Guardrails & AI Safety

How the security, guardrails & ai safety landscape is shaped, and how the Quest tests it.

## The landscape at a glance

| Tool | Tier | License | Focus | Notes |
|------|------|---------|-------|-------|
| Guardrails AI | A | Apache-2.0 | Guardrail Framework | 6.8K stars; 70+ Hub validators; Python-centric; RAIL specs |
| NVIDIA NeMo Guardrails | A | Apache-2.0 | Guardrail Framework | 5.6K stars; dialog/input/output/retrieval/execution rails; C |
| Lakera Guard (Check Point) | A | Proprietary | Runtime Firewall | Gandalf dataset; sub-50ms; acquired by Check Point |
| Future AGI Protect | A | Apache-2.0 (gateway) | Gateway + Guardrails | 18+ scanners; agent tool-call rails; 65ms text / 107ms image |
| Prompt Security (SentinelOne) | A | Proprietary | Runtime Defense | Inline prompt/response inspection; acquired by SentinelOne 2 |
| Arthur AI Shield | A | Proprietary | LLM Firewall | Prompt injection, hallucination scoring; SOC 2 compliant; RE |
| AWS Bedrock Guardrails | A | Proprietary | Managed Guardrails | AWS-native; content filtering; topic denial; PII; per text-u |
| Azure AI Content Safety | A | Proprietary | Content Moderation | Enterprise; toxicity, hate, self-harm, violence; multilingua |
| Google Model Armor | A | Proprietary | Input/Output Filtering | GCP-native; prompt injection, data leakage; part of Vertex A |
| OpenAI Moderation API | A | Free (hosted) | Content Moderation | Mass adoption; text + image toxicity; 13+ harm categories; f |
| Llama Guard 3 (Meta) | A | Open weights | Safety Classifier | 14-category content safety; 3.8B parameters; prompt-based ta |
| Patronus AI | A | Proprietary + OSS (Lynx) | Evaluator + Guardrails | Regulated domains; hallucination, PII, copyright, finance; L |
| Galileo | A | Proprietary + OSS (Agent Control) | Eval + Runtime Protection | Luna-2 SLM evaluators; 0.95 F1; 98% lower cost than LLM judg |
| Fiddler AI | A | Proprietary | Observability + Guardrails | $100M raised; hallucination, safety, jailbreak, ML drift; <1 |
| Confident AI (DeepTeam) | A | Apache-2.0 (OSS) | Red Teaming + Eval | 50+ vulnerabilities; OWASP/NIST mapping; pytest integration; |
| Protect AI (Palo Alto / Prisma AIRS) | A | Proprietary | Full Lifecycle Security | $108M raised; model scanning, red teaming, runtime firewall; |
| HiddenLayer | A | Proprietary | AI Security Posture | $56M raised; model scanning, runtime threat detection, AIDR; |
| WitnessAI | A | Proprietary | Network-level Governance | $58M raised (Series B); behavioral intent analysis, policy r |
| Credo AI | A | Proprietary | AI Governance Platform | $41M raised; AI inventory, risk assessments, policy packs; E |

## How the Quest tests a tool

Same harness for all entries; the judge was frozen before any tool ran:

```
Adapter[frozen CategoryAdapter contract]
  ├── setup()    → install, configure
  ├── load()     → ingest workload
  ├── await_ready() → async barrier
  ├── query()    → run test, get response
  └── teardown() → cleanup, measure
       ↓
Telemetry: latency · tokens · $ · ops notes
       ↓
Grading: deterministic + LLM judge (frozen prompts)
       ↓
Raw results JSON (published)
```

The `await_ready()` barrier is where async designs get their cost measured instead of hidden.

## License

Content is licensed CC BY 4.0 — share and adapt with attribution to **ArdurAI / Security, Guardrails & AI Safety Almanac**.
