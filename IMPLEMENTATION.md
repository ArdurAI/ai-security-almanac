# Implementation Guide

How the security almanac is built, how to add a security tool, how to update an edition, and how the data pipeline works.

## Table of Contents

1. [Repository Structure](#repository-structure)
2. [The Data Pipeline](#the-data-pipeline)
3. [Adding a New Security Tool](#adding-a-new-security-tool)
4. [Updating an Edition](#updating-an-edition)
5. [The Roster JSON Schema](#the-roster-json-schema)
6. [Directory Conventions](#directory-conventions)
7. [Building the Security Adapter](#building-the-security-adapter)
8. [Automation](#automation)

---

## Repository Structure

```
ai-security-almanac/
├── README.md                          # Project overview + roster at a glance
├── INTENT.md                          # Philosophy, design principles, governance
├── IMPLEMENTATION.md                  # This file
├── TESTING.md                         # Benchmark methodology, harness details
├── TROUBLESHOOTING.md                 # Common issues, debugging, FAQ
├── CONTRIBUTING.md                    # How to contribute
├── architecture.md                    # Stack architecture + test philosophy
├── SETUP.md                           # How to push to GitHub
├── .gitignore
│
├── editions/                          # Monthly editions
│   └── 2026-06.md                     # Founding edition
│
├── benchmarks/                        # Benchmark results (rolling)
│   └── (populated as results land)
│
├── methodology/
│   └── benchmark-harness.md           # Detailed harness spec
│
├── data/
│   └── roster.json                    # Machine-readable catalog (68 tools)
│
├── tools/                             # Per-tool deep-dive pages (placeholder)
│   └── (populated as deep-dives are written)
│
└── assets/                            # Charts, diagrams, screenshots
    └── (populated by editions)
```

## The Data Pipeline

The almanac data flows through four stages:

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│  Discovery      │────▶│  Triage         │────▶│  Research       │────▶│  Publication    │
│  (find tools)   │     │  (decide entry) │     │  (deep dive)    │     │  (write edition) │
└─────────────────┘     └─────────────────┘     └─────────────────┘     └─────────────────┘
```

### Stage 1: Discovery

Security tools are discovered through:
- **Monthly research swarm**: 8-10 parallel agents search for new security, guardrails, and safety tools
- **Community submissions**: Issues, PRs, email, social media, red-team community channels
- **Vendor announcements**: Product launches, funding rounds, compliance certifications, major releases
- **GitHub trending**: New repos with significant star growth in the AI security / safety space
- **Conference talks**: DEF CON, Black Hat, NeurIPS AI safety, OWASP, and major security events
- **Advisory publications**: NIST, ENISA, CISA AI security guidance that mentions specific tools

### Stage 2: Triage

A tool enters the roster if it meets ALL of these criteria:
1. **Seriousness**: Not a toy/demo. Must have a real use case, real users, or real funding. Must actually perform a security function (input filtering, output filtering, PII detection, prompt injection defense, etc.).
2. **Activity**: Last push or release within 6 months. Exceptions for "stable/mature" tools that are widely deployed.
3. **Documentation**: Must have a README, docs, or at least a landing page explaining what it does, how to configure it, and what attacks it covers.
4. **Accessibility**: Must be accessible to test (open source, free tier, or evaluation license available). Security tools that require an enterprise-only sales process with no evaluation are excluded.
5. **Scope**: Must fit the category definition. A general-purpose AI platform with a minor security checkbox does not enter the security category unless the security function is the primary focus.

A tool is **excluded** if:
- It's a fork of another tool with no meaningful security divergence
- It's a thin wrapper around another tool with no added security value
- It has no users, no community, and no evidence of real-world deployment
- It requires an enterprise-only license with no evaluation path
- It is a "security theater" tool — no actual detection or prevention mechanism

### Stage 3: Research

For each new tool, we collect:
- Name, type, license, language, GitHub URL, stars
- Last push date, release cadence
- Key security features and differentiators (e.g., "multi-turn conversation analysis", "on-premise deployment", "OWASP Top 10 coverage")
- Known bypasses and sharp edges (from smoke gate and red-team exercises)
- Compliance certifications and standards mapping (NIST AI RMF, EU AI Act, SOC 2, ISO 27001)
- Community health (issues, PRs, maintainer responsiveness, security advisory response time)
- Pricing model (per request, per token, per seat, self-hosted licensing)

This data is stored in `data/roster.json` and summarized in the edition.

### Stage 4: Publication

The edition is a markdown file that includes:
- Landscape at a glance table (guardrails, prompt injection detectors, PII scrubbers, output filters, etc.)
- Per-tool findings and trends (new bypass techniques, emerging false positive patterns)
- New tools added and tools removed
- Notable releases and acquisitions
- Compliance landscape updates (new standards, new mapping requirements)
- Quest diary (what was tested this month, what bypasses were discovered)

## Adding a New Security Tool

### Step 1: Verify the tool meets triage criteria

Check: seriousness, activity, documentation, accessibility, scope. Pay special attention to whether the tool actually performs a security function and whether it can be tested without an enterprise sales process.

### Step 2: Add to the roster JSON

Edit `data/roster.json` and add the tool to the security category:

```json
{
  "name": "ToolName",
  "type": "Guardrail Framework",
  "license": "License",
  "region": "Region",
  "tier": "A|B|C",
  "notes": "One-line description and key differentiators (e.g., on-premise, multi-turn, low-latency)"
}
```

**Tier assignment rules**:
- **Tier A**: Market leader, widest adoption, or strongest technical merit. Must be actively maintained, have real production usage, and demonstrate measurable effectiveness (e.g., published detection rates, compliance certifications). Must pass the smoke gate with no critical failures.
- **Tier B**: Solid option, actively maintained, but not the market leader. Good for specific use cases (e.g., on-premise only, specialized for PII, specialized for prompt injection). Must pass the smoke gate.
- **Tier C**: Niche, early-stage, or specialized. Worth knowing about but not a default choice. May have limited documentation, narrow scope, or known bypasses. Still must pass the smoke gate; if it fails, it is noted with a blocker.

### Step 3: Update the edition

Add the tool to the security category section in `editions/YYYY-MM.md`. If the tool is Tier A, add it to the roster-at-a-glance table in the README.

### Step 4: Build the security adapter

Before the tool is officially "in," it must have a working adapter (see [Building the Security Adapter](#building-the-security-adapter)). The adapter must implement the security-specific contract: `configure_policy()`, `send_benign()`, `send_adversarial()`, and `get_block_decision()`.

### Step 5: Run the smoke gate

Before the tool is officially "in," it must pass the smoke gate (see TESTING.md). The security smoke gate is a 3-turn scenario: configure a basic policy, send a benign prompt, send an adversarial prompt and verify it is blocked. If it fails, document the failure in the edition and assign it to Tier C with a note about the blocker.

## Updating an Edition

### Monthly update checklist

```
□ Check for new security tools (discovery phase)
□ Triage new tools (add to roster or reject)
□ Update metadata for existing tools (stars, last push, releases, compliance certifications)
□ Flag tools for removal (dead/abandoned, known vulnerabilities with no fix)
□ Run smoke gate for new tools
□ Run benchmark updates for re-tested tools (especially after new attack pattern releases)
□ Draft the edition markdown
□ Update README roster-at-a-glance
□ Commit and push
```

### Edition markdown template

```markdown
# Edition YYYY-MM — [Title]

*Research conducted YYYY-MM-DD. [Context about this month — e.g., new OWASP guidance, major bypass published, new compliance requirement].*

## The landscape at a glance

| Tool Count | New This Month | Notable Changes | Compliance Updates |
|------------|----------------|-----------------|--------------------|

## Security Findings — [Theme]

### Tier A roster
[table]

### Findings
[bullets — e.g., "Tool X now supports EU AI Act Article 15; Tool Y had a bypass published on GitHub"]

## New attack patterns this month

[summary of new jailbreak techniques, encoding bypasses, or multi-turn attacks discovered in the research period]

## Quest diary — [Month] [Year]

- [what was tested — e.g., "Ran OWASP LLM Top 10 suite on 12 Tier A tools; discovered 3 new bypasses in Tool Z"]

## Coming next month

[what's planned — e.g., "Stress test all Tier A tools under 10x burst load; evaluate new compliance mapping for NIST AI RMF v2"]

## License
Content is licensed CC BY 4.0.
```

## The Roster JSON Schema

```json
{
  "meta": {
    "name": "AI Security Almanac Roster",
    "version": "YYYY-MM",
    "generated_at": "ISO-8601 timestamp",
    "total_tools": number,
    "categories": 1,
    "research_method": "description"
  },
  "categories": {
    "security-guardrails": {
      "name": "Security, Guardrails & AI Safety",
      "description": "AI security tools: input validation, prompt injection defense, output filtering, PII detection, compliance guardrails, and safety alignment",
      "estimated_total": number,
      "tools": [
        {
          "name": "Tool Name",
          "type": "Guardrail Framework | Prompt Injection Detector | PII Scrubber | Output Filter | Safety Aligner | Policy Engine",
          "license": "License",
          "region": "Region",
          "tier": "A|B|C",
          "notes": "Description"
        }
      ]
    }
  }
}
```

**Field definitions**:
- `name`: The tool's common name. Use the name the tool calls itself.
- `type`: What kind of security tool is it? Common types: `Guardrail Framework`, `Prompt Injection Detector`, `PII Scrubber`, `Output Filter`, `Safety Aligner`, `Policy Engine`, `Content Moderator`, `Adversarial Tester`.
- `license`: The primary license. Use SPDX identifiers where possible. For proprietary tools, use `Proprietary`.
- `region`: Where the tool is primarily developed (US, EU, China, Global, etc.).
- `tier`: A, B, or C (see tier rules above)
- `notes`: One-line description with key differentiators. Keep under 100 chars. Mention deployment model (on-premise, cloud, hybrid) if relevant.

## Directory Conventions

### `editions/`
- One file per month: `YYYY-MM.md`
- Never delete old editions. The history is part of the record.
- New editions are appended; old editions are never rewritten.
- Editions may include security advisories (e.g., "Tool X had a bypass published on 2026-05-15; the almanac re-tested and confirmed it on 2026-05-20").

### `data/`
- `roster.json` is the single source of truth for the tool catalog.
- It is machine-generated from the research process.
- It should be valid JSON at all times.

### `benchmarks/`
- One file per benchmark run: `security-<suite>-<date>.md`
- Raw JSON files alongside the markdown: `security-<suite>-<date>.json`
- Raw data is never deleted. It is the audit trail.
- For adversarial tests, the raw JSON includes the full attack prompt, the tool's response (block/allow), and the confidence score if available.

### `tools/`
- One file per tool: `<name>.md`
- Contains deep-dive analysis: setup experience, benchmark results, known bypasses, false positive examples, compliance mapping, comparison with peers
- Populated as deep-dives are written (not all tools have a page immediately)
- A security tool deep-dive should include: "How to bypass it" (responsibly disclosed), "What false positives look like", "How to configure it for production"

### `assets/`
- Images, charts, diagrams referenced by editions and benchmarks
- Named descriptively: `smoke-latency-2026-06.png`, `false-positive-rate-2026-06.png`, `roc-curve-toolname-2026-06.png`

### `methodology/`
- The benchmark harness specification
- Frozen before any results are generated
- Changes require an RFC and a public announcement
- Includes the attack prompt library, the benign prompt library, the scoring rubric, and the compliance mapping criteria

## Building the Security Adapter

When a new security tool is added to the roster and is ready for benchmarking, an adapter must be built. The adapter is the bridge between the tool's API and the harness's fixed security interface.

### The SecurityAdapter contract

```python
class SecurityAdapter:
    def setup(self) -> None:
        """Install, configure, and start the tool."""
        pass
    
    def configure_policy(self, policy_config) -> None:
        """Configure the security policy (e.g., block prompt injection, PII redaction level)."""
        pass
    
    def send_benign(self, prompt) -> Response:
        """Send a legitimate prompt and return the response (should NOT be blocked)."""
        pass
    
    def send_adversarial(self, attack_prompt) -> Response:
        """Send an adversarial prompt and return the response (SHOULD be blocked)."""
        pass
    
    def get_block_decision(self, response) -> Decision:
        """Parse the tool's response to determine if it was blocked or allowed."""
        pass
    
    def teardown(self) -> None:
        """Clean up, measure resource usage."""
        pass
```

### Adapter rules

1. The adapter must be **pure** — it should not modify the tool's detection logic, only interface with it.
2. The adapter must be **documented** — every step should be explainable in plain English (e.g., "We set the prompt injection threshold to 0.7 because the docs say that is the recommended production setting").
3. The adapter must be **reproducible** — running it twice on the same machine should produce the same policy configuration and same detection behavior.
4. The adapter must be **isolated** — it should not depend on other tools' adapters.
5. The adapter code is **published** in the benchmark harness repo (separate from the almanac repo).

### Example adapters (pseudocode)

#### OWASP LLM Top 10 test adapter

```python
class OWASPAdapter(SecurityAdapter):
    def __init__(self, tool_name, config):
        self.tool = tool_name
        self.config = config
    
    def setup(self):
        if self.tool == "guardrails-ai":
            self.client = GuardrailsClient(config=self.config)
        elif self.tool == "nemo-guardrails":
            self.client = NeMoGuardrails(config_path=self.config)
    
    def configure_policy(self, policy_config):
        # Configure for OWASP Top 10 coverage
        self.client.set_policy(
            llm01_prompt_injection=True,
            llm02_insecure_output_handling=True,
            llm03_training_data_poisoning=False,  # not a runtime guardrail
            # ... etc
        )
    
    def send_adversarial(self, attack_prompt):
        return self.client.validate_input(attack_prompt)
    
    def get_block_decision(self, response):
        return Decision(
            blocked=response.is_blocked,
            confidence=response.confidence_score,
            reason=response.block_reason
        )
```

#### Gandalf prompt injection test adapter

```python
class GandalfAdapter(SecurityAdapter):
    def __init__(self, tool_name, config):
        self.tool = tool_name
        self.config = config
    
    def setup(self):
        self.client = load_tool_client(self.tool, self.config)
    
    def configure_policy(self, policy_config):
        # Gandalf tests require "system prompt must not be revealed" policy
        self.client.set_policy(
            system_prompt_exfiltration="block",
            instruction_hierarchy="strict"
        )
    
    def send_adversarial(self, attack_prompt):
        # The attack prompt is designed to extract the system prompt
        return self.client.validate_input(attack_prompt)
    
    def get_block_decision(self, response):
        # Gandalf "win" means the attack succeeded (system prompt leaked)
        # So we want the tool to BLOCK the attack
        leaked = contains_system_prompt(response.raw_output)
        return Decision(
            blocked=not leaked,  # Blocked = good (no leak)
            confidence=1.0 if not leaked else 0.0,
            reason="system_prompt_leaked" if leaked else "blocked"
        )
```

#### Prompt injection test adapter (custom adversarial suite)

```python
class PromptInjectionAdapter(SecurityAdapter):
    def __init__(self, tool_name, config):
        self.tool = tool_name
        self.config = config
    
    def setup(self):
        self.client = load_tool_client(self.tool, self.config)
    
    def configure_policy(self, policy_config):
        # Standard production policy: block direct injection, indirect injection, jailbreaks
        self.client.set_policy(
            direct_injection="block",
            indirect_injection="block",
            jailbreak="block",
            encoding_bypass="block"
        )
    
    def send_adversarial(self, attack_prompt):
        return self.client.validate_input(attack_prompt)
    
    def get_block_decision(self, response):
        return Decision(
            blocked=response.is_blocked,
            confidence=response.confidence,
            reason=response.block_reason
        )
```

## Automation

### Monthly update cron

The monthly update is run by a scheduled job:
- **Trigger**: `cron` expression `0 7 15 * *` (monthly, 15th at 7:00 AM)
- **Action**: Runs a research agent to discover new security tools, update metadata, draft the next edition, and check for new attack patterns
- **Output**: Commits to the repo with the updated roster and new edition

### GitHub Actions (optional)

For automatic metadata refresh (GitHub stars, last push dates, compliance certification updates), a GitHub Actions workflow can be configured:

```yaml
name: Monthly Security Metadata Refresh
on:
  schedule:
    - cron: '0 7 1 * *'
  workflow_dispatch:
jobs:
  refresh:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Refresh metadata
        run: python scripts/refresh_security_metadata.py
      - name: Commit and push
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add -A
          git commit -m "Monthly security metadata refresh: $(date +%Y-%m)" || echo "No changes"
          git push
```

## License

Content: CC BY 4.0  
Code: MIT
