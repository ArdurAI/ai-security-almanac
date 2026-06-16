# Troubleshooting & Debugging

How to understand the security almanac codebase, debug issues, and resolve common problems when working with the almanac.

## Table of Contents

1. [Understanding the Codebase](#understanding-the-codebase)
2. [Common Issues](#common-issues)
3. [Debugging the Data Pipeline](#debugging-the-data-pipeline)
4. [Debugging Benchmark Runs](#debugging-benchmark-runs)
5. [FAQ](#faq)
6. [Getting Help](#getting-help)

---

## Understanding the Codebase

### High-level flow

```
Research Agents → Research Output (Markdown) →
  Python Script → roster.json (Structured Data) →
    Manual Review → Edition Markdown →
      Git Commit → GitHub Publication
```

### Key files and their roles

| File | Role | When to read it |
|------|------|-----------------|
| `README.md` | Project overview, quick reference | First thing you read |
| `INTENT.md` | Philosophy, why we do things this way | When you disagree with a decision or need to understand the security-specific design principles |
| `IMPLEMENTATION.md` | How things are built, how to add security tools | When you want to contribute or build an adapter |
| `TESTING.md` | Benchmark methodology, scoring, failure taxonomy | When you want to reproduce or challenge a security result |
| `TROUBLESHOOTING.md` | This file | When something is broken or a benchmark result doesn't make sense |
| `architecture.md` | Stack architecture diagram | When you want to understand the big picture of how security tools fit into the AI stack |
| `editions/YYYY-MM.md` | Monthly snapshot of the security landscape | When you want historical data or want to know when a bypass was first discovered |
| `data/roster.json` | Machine-readable catalog | When you want to query or analyze the security tool data |
| `methodology/benchmark-harness.md` | Harness specification | When you want to build a security adapter or run adversarial benchmarks |

### The data model

The almanac is fundamentally a **directed graph** of data:

```
Research findings → Tool metadata → Roster JSON → Edition Markdown → README
                                      ↓
                               Benchmark results → Per-tool pages
```

- **Research findings** are the raw output of the research swarm. They're saved in `research/` (not in the public repo).
- **Tool metadata** is extracted from research and stored in `data/roster.json`.
- **Roster JSON** is the single source of truth. Everything else derives from it.
- **Edition markdown** is human-written based on the roster and research.
- **README** is auto-generated from the roster and the latest edition.

### Understanding `data/roster.json`

This is the most important file in the repo. It is the single source of truth.

**Structure**:
```json
{
  "meta": { ... },
  "categories": {
    "security-guardrails": {
      "name": "Security, Guardrails & AI Safety",
      "description": "...",
      "estimated_total": N,
      "tools": [
        { "name": "...", "type": "...", "license": "...", "tier": "A|B|C", "notes": "..." }
      ]
    }
  }
}
```

**How to query it**:
```bash
# Find all Tier A security tools
jq '.categories."security-guardrails".tools[] | select(.tier == "A") | .name' data/roster.json

# Count security tools by tier
jq '.categories | to_entries[] | .value.tools | group_by(.tier) | map({tier: .[0].tier, count: length})' data/roster.json

# Find all MIT-licensed security tools
jq '.. | objects | select(.license == "MIT") | .name' data/roster.json

# Find all on-premise tools (based on notes field)
jq '.categories."security-guardrails".tools[] | select(.notes | contains("on-premise")) | .name' data/roster.json
```

### The edition markdown

Editions are **human-written** summaries, not machine-generated. They are based on the roster but include analysis, interpretation, and narrative that a machine can't produce.

**How editions are structured**:
1. Front matter: date, research method, context (e.g., "New OWASP guidance published this month")
2. Landscape at a glance: summary table
3. Security findings: per-tool findings, bypass discoveries, false positive patterns
4. New attack patterns: summary of newly discovered jailbreaks or bypass techniques
5. Compliance updates: new standards, new mapping requirements
6. Quest diary: what was tested this month (e.g., "Ran encoding bypass suite on 12 tools; 3 had critical failures")
7. Cross-category findings: patterns that span categories (e.g., "All cloud-hosted guardrails had higher latency than self-hosted")

### The benchmark harness (separate repo)

The actual benchmark code lives in a separate repository. The almanac repo contains:
- The methodology specification
- The results (JSON + markdown)
- The adapter interface definitions

The harness repo contains:
- The runner code
- The security adapter implementations (OWASP, Gandalf, custom adversarial)
- The judge model integration (for semantic evaluation)
- The telemetry collector (latency, FPR, FNR, cost)
- The attack prompt library and benign prompt library

**Why separate?** Because the harness is code that runs, and the almanac is data that is published. They have different lifecycles and different audiences. The harness also contains sensitive attack prompts that we don't want to casually expose to automated scraping.

## Common Issues

### Issue: `roster.json` is invalid JSON

**Symptoms**:
- `jq` fails to parse it
- GitHub Actions fails on JSON validation
- Python `json.load()` raises `JSONDecodeError`

**Diagnosis**:
```bash
python3 -c "import json; json.load(open('data/roster.json'))"
```

**Resolution**:
1. Find the line with the error: `python3 -m json.tool data/roster.json`
2. Common causes: trailing commas, unescaped quotes, incorrect nesting
3. Fix the JSON and re-validate
4. Consider using a JSON linter in your editor

### Issue: Edition markdown has broken links

**Symptoms**:
- Links to tools return 404
- Links to benchmarks don't exist yet
- Relative links work locally but break on GitHub

**Diagnosis**:
```bash
# Check all links in the repo
find . -name "*.md" -exec grep -oP '\[.*?\]\(.*?\)' {} + | grep -v "http" | grep -v "mailto"
```

**Resolution**:
1. For internal links, use relative paths: `../data/roster.json`
2. For external links, verify the URL is correct (security tool homepages change frequently)
3. For tools without a per-tool page yet, link to their homepage or GitHub repo
4. Run a link checker as part of CI

### Issue: Tier assignment is wrong

**Symptoms**:
- A tool is Tier A but has no production usage or known bypasses
- A tool is Tier C but is widely adopted in regulated industries
- A tool's tier changed without explanation

**Diagnosis**:
1. Check the tier assignment rules in `IMPLEMENTATION.md`
2. Verify the tool's adoption, activity, community health, and compliance certifications
3. Check the edition notes for the rationale (e.g., "Downgraded from Tier A to Tier B due to critical bypass published in March 2026")

**Resolution**:
1. File an issue with evidence (GitHub stars, last push, production references, compliance certifications, bypass publications)
2. The tier will be reviewed in the next edition cycle
3. Tiers are not changed mid-edition; they are updated at edition boundaries

### Issue: Benchmark results can't be reproduced

**Symptoms**:
- Running the harness produces different detection rates
- The adapter fails with a different tool version
- The judge model is unavailable
- False positive rates differ significantly

**Diagnosis**:
1. Check the `results.json` metadata for the exact commit, seed, hardware, and policy configuration
2. Check if the tool version has changed since the published run (security tools update frequently to patch bypasses)
3. Verify the judge model is accessible
4. Check if the attack prompt library or benign prompt set has been updated (new prompts are added quarterly)

**Resolution**:
1. Use the exact commit and dependencies from the results metadata
2. If the tool version changed, the results are for a different version — this is expected; check the version notes in the edition
3. If the attack prompt library changed, use the archived version from the benchmark run date
4. If the judge model changed, that's a methodology issue — file an issue

### Issue: Research agent missed a security tool

**Symptoms**:
- A well-known security tool is not in the roster
- A tool from a specific region (e.g., EU, China) is missing
- A newly launched security tool is not in the latest edition

**Diagnosis**:
1. Check if the tool meets triage criteria in `IMPLEMENTATION.md` (seriousness, activity, documentation, accessibility, scope)
2. Check if it was added in a previous edition and later removed (e.g., for inactivity or because it was acquired and shut down)
3. Check if it falls outside the search scope (e.g., it's a general-purpose AI platform with a minor security feature, not a security-focused tool)

**Resolution**:
1. File an issue with the tool name, URL, and evidence of adoption/activity/compliance certifications
2. The tool will be triaged for the next edition
3. If it meets criteria, it will be added

### Issue: Monthly update cron failed

**Symptoms**:
- No new edition was published on the 15th
- The cron job is missing from the scheduler
- The research agent timed out (security research often takes longer due to compliance checks)

**Diagnosis**:
```bash
# Check cron status
cron status

# Check the cron job list
# (use the Kimi Work cron interface)
```

**Resolution**:
1. Check if the cron job is still registered
2. Check if the research agent timed out (increase timeout if needed; security research often requires 2x the time of general infrastructure research)
3. Manually trigger the update if the cron missed a cycle
4. Check the workspace path in the cron job configuration

### Issue: GitHub push fails

**Symptoms**:
- `git push` returns 403 or 401
- The remote is not configured
- The branch is behind origin

**Diagnosis**:
```bash
git remote -v
git status
git log --oneline -5
```

**Resolution**:
1. Verify the remote URL is correct: `git remote set-url origin https://github.com/ArdurAI/...`
2. Verify GitHub CLI auth: `gh auth status`
3. If behind origin, pull first: `git pull origin main`
4. If there are conflicts, resolve them manually

### Issue: Adapter fails on adversarial input encoding

**Symptoms**:
- The adapter crashes when processing Base64, Unicode, or zero-width characters
- The tool's API returns an error for encoded inputs
- The adapter returns a parsing error instead of a block/allow decision

**Diagnosis**:
1. Check if the adapter's `send_adversarial()` method correctly handles encoded input (it should pass the raw string to the tool, not decode it)
2. Check if the tool's API has a maximum input length that encoded inputs exceed
3. Check if the tool's API rejects non-ASCII characters

**Resolution**:
1. The adapter should pass the raw attack prompt string to the tool without modification
2. If the tool's API has length limits, document them in the adapter notes
3. If the tool's API rejects non-ASCII characters, document it as a limitation and note it in the per-tool page

### Issue: Benchmark results inconsistent due to evolving attack patterns

**Symptoms**:
- Detection rates change significantly between runs without a tool update
- False positive rates fluctuate
- The attack prompt library was updated between runs

**Diagnosis**:
1. Check the SHA-256 hash of the attack prompt library in the results metadata
2. Check if the benign prompt set was updated (new legitimate prompts added)
3. Check if the tool's policy or threshold changed between runs

**Resolution**:
1. Attack prompt libraries are updated quarterly. Results from different quarters are not directly comparable without normalization.
2. For longitudinal comparison, use the archived attack prompt sets from each quarter.
3. If you need to compare tools across time, re-run all tools with the same attack prompt set.

### Issue: Compliance standard version mismatches

**Symptoms**:
- A tool is marked as "NIST AI RMF compliant" but the compliance checklist references an outdated version
- The EU AI Act mapping is wrong because the tool was tested against a draft version
- SOC 2 mapping fails because the tool doesn't support the latest Type II requirements

**Diagnosis**:
1. Check the compliance checklist version in the results metadata
2. Check the tool's documentation for the compliance standard version it claims to support
3. Check the edition notes for compliance updates

**Resolution**:
1. Compliance checklists are updated quarterly. If a tool was tested against an outdated checklist, it will be re-tested in the next cycle.
2. File an issue with the correct standard version and evidence.
3. The compliance score is published separately from the composite score; it does not affect rankings directly.

## Debugging the Data Pipeline

### Research output → roster.json

**Problem**: Research agents produce markdown, but the roster JSON is incomplete or wrong.

**Debug steps**:
1. Read the research output files in `research/` (local workspace, not in the repo)
2. Check if the Python extraction script correctly parsed the tool tables
3. Check if tools were dropped during triage (check the triage log)
4. Verify the JSON schema is correct (see `IMPLEMENTATION.md`)

**Common bugs**:
- Tool names with special characters break JSON parsing → Escape them properly
- Tools with no tier get dropped → Default to Tier C if unsure
- Tools with no notes get empty strings → Add a minimal note mentioning the security function
- Tools with no compliance data get dropped → Add compliance data if available; default to "unknown" if not

### roster.json → edition markdown

**Problem**: The edition doesn't reflect the roster.

**Debug steps**:
1. Compare the tool counts in the roster vs. the edition
2. Check if the edition was written before the roster was updated
3. Check if tools were manually edited in the edition but not in the roster

**Resolution**:
1. The edition should be derived from the roster, not the other way around
2. If the edition has manual additions, ensure they are also in the roster
3. The edition is a human-readable summary; the roster is the source of truth

### Edition markdown → README

**Problem**: The README roster-at-a-glance doesn't match the latest edition.

**Debug steps**:
1. Check which edition is referenced in the README
2. Check if the README was updated after the edition was published

**Resolution**:
1. The README should always reference the latest edition
2. Update the README when a new edition is published
3. Consider automating README updates from the roster JSON

## Debugging Benchmark Runs

### The adapter fails

**Symptoms**:
- `setup()` crashes
- `configure_policy()` throws an exception
- `send_adversarial()` returns an error instead of a block/allow decision

**Debug steps**:
1. Run the adapter in isolation (without the harness)
2. Check the tool's documentation for setup requirements (API keys, environment variables, policy YAML syntax)
3. Check if environment variables are set (e.g., `GUARDRAILS_API_KEY`, `NEMO_CONFIG_PATH`)
4. Check if the tool version matches what the adapter expects (security tools update frequently)

**Common fixes**:
- Missing API key → Set the environment variable
- Wrong policy syntax → Update the adapter to match the new syntax; document the breaking change
- Wrong tool version → Update the adapter or pin the version
- Dependency conflict → Use a virtual environment or container
- Policy engine not started → Start the policy engine (some tools require a separate service)

### The canary fails

**Symptoms**:
- The no-tool baseline produces unexpected blocks or allows
- The latency is not zero
- The cost is not zero

**Debug steps**:
1. Check if the canary adapter has any side effects (it should be a pure no-op)
2. Check if the harness is instrumenting the wrong functions
3. Check if the benign prompt set or attack prompt set has been corrupted

**Resolution**:
1. If the canary produces blocks, the adapter or harness is broken — fix it
2. If the canary has latency, the instrumentation is wrong — fix it
3. This is a critical failure — the entire batch is invalid

### Results are inconsistent

**Symptoms**:
- Same tool, same test, different results across runs
- Detection rates vary by more than the confidence interval
- False positive rates fluctuate

**Debug steps**:
1. Check if the tool has non-deterministic behavior (e.g., stochastic detection thresholds, model-based detection with temperature > 0)
2. Check if the hardware was different between runs
3. Check if the tool version changed between runs
4. Check if the policy configuration drifted between runs

**Resolution**:
1. Set temperature to 0 for all LLM-based detection in the adapter
2. Record hardware specs in the results metadata
3. Pin tool versions and record them
4. Record the exact policy configuration in the results metadata
5. If the tool is inherently non-deterministic (e.g., uses a neural classifier with randomness), increase the number of runs and report the variance

## FAQ

### Q: Why is tool X not in the roster?

A: Either it doesn't meet triage criteria, it hasn't been discovered yet, or it was removed for inactivity. File an issue with evidence and we'll triage it.

### Q: Why did tool X's score change?

A: Either the tool was updated (e.g., a new bypass was patched), the methodology was refined (e.g., new attack prompts added), or we found a bug in our previous test. All three are valid reasons. Check the edition notes for the rationale.

### Q: Can I run the benchmarks myself?

A: Yes. The harness is published separately. Clone it, install dependencies, and run the adapter for the tool you want to test. See `TESTING.md` for reproducibility instructions. Note that some attack prompts are sensitive; access may require approval.

### Q: How do I challenge a ranking?

A: File an issue with specific evidence. Check the raw results JSON, the adapter code, and the attack prompts. If you find a real problem (e.g., the adapter was misconfigured, or the attack prompt was malformed), we'll re-run or update the methodology.

### Q: Can I add a tool to the roster?

A: Yes. See `CONTRIBUTING.md` for instructions. The tool must meet triage criteria and pass the smoke gate.

### Q: Why are there separate category almanacs?

A: Each category is deep enough to warrant its own dedicated repo with per-tool pages, category-specific benchmarks, and focused community. The parent almanac is the master catalog. Security is especially deep because it requires adversarial testing, compliance mapping, and red-team expertise.

### Q: How often are benchmarks re-run?

A: Standard (OWASP, Gandalf): every quarter for each tool. Custom adversarial: every quarter (attack patterns evolve). Stress: annually. Compliance mapping: quarterly. If a tool releases a major version or a critical bypass is published, we may re-run early.

### Q: What's the difference between Tier A, B, and C?

A: Tier A = market leader or strongest technical merit, with production usage and compliance certifications. Tier B = solid option, specific use cases (e.g., on-premise only, PII-focused). Tier C = niche, early-stage, or specialized. See `IMPLEMENTATION.md` for full rules.

### Q: Can vendors sponsor the almanac?

A: No. The almanac is independently funded. Sponsorship would compromise the core mission. Vendors can improve their scores by actually improving their tools (patching bypasses, reducing false positives, improving compliance mapping).

### Q: How do you handle responsible disclosure of bypasses?

A: We follow a 90-day responsible disclosure policy. When we discover a bypass during testing, we notify the vendor privately, give them 90 days to patch, and then publish the bypass in the edition and per-tool page. If the vendor does not respond, we publish after 90 days regardless. The bypass is classified in the failure mode taxonomy.

### Q: Why do some tools have high accuracy but low composite scores?

A: Because accuracy is only 30% of the composite. A tool with 99% detection rate but 15% false positive rate, 200ms latency, and high ops burden will score poorly on the other dimensions. In production, a high-FPR tool is often disabled, so the composite reflects real-world value.

### Q: What is the "fixed false positive rate" comparison?

A: We compare tools at a fixed 1% false positive rate (FPR). This means we tune each tool's threshold until it produces exactly 1% FPR on the benign prompt set, then measure its block rate on the attack set. This ensures fair comparison: we don't compare a tool at 0.1% FPR against another at 10% FPR.

## Getting Help

### File an issue

GitHub issues are the primary support channel. Use the appropriate template:

- **Tool request**: "Add [Tool Name] to Security Almanac"
- **Data correction**: "[Tool Name] metadata is wrong: [what's wrong]"
- **Benchmark challenge**: "Challenge [Tool Name] ranking on [Dimension]: [evidence]"
- **Bypass report**: "Bypass in [Tool Name]: [description] — responsibly disclosed on [date]"
- **Bug report**: "[Bug description] in [file/process]"
- **Feature request**: "[Feature description] for [use case]"

### Discussion

GitHub Discussions are for:
- General questions about the almanac
- Sharing experiences with security tools on the roster
- Proposing methodology changes (e.g., new attack benchmarks, new compliance standards)
- Community announcements (e.g., "I published a new bypass for Tool X")

### Email

For private or sensitive inquiries (e.g., responsible disclosure before the 90-day window): Use the contact info in the ArdurAI org profile.

## License

Content: CC BY 4.0
