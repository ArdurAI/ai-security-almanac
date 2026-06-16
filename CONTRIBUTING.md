# Contributing to the Security Almanac

How to add security tools, fix data, challenge rankings, and improve the security evaluation methodology.

## Table of Contents

1. [Ways to Contribute](#ways-to-contribute)
2. [Adding a New Security Tool](#adding-a-new-security-tool)
3. [Fixing Data](#fixing-data)
4. [Challenging a Ranking](#challenging-a-ranking)
5. [Improving the Methodology](#improving-the-methodology)
6. [Reporting Bypasses](#reporting-bypasses)
7. [Code of Conduct](#code-of-conduct)
8. [License](#license)

---

## Ways to Contribute

You can contribute to the security almanac in several ways:

| Contribution Type | What you do | Impact |
|-------------------|-------------|--------|
| **Add a tool** | File an issue with a new security tool | Expands the roster |
| **Fix data** | Correct incorrect metadata (license, tier, compliance certifications) | Improves accuracy |
| **Challenge a ranking** | Provide evidence that a detection rate or false positive rate is wrong | Drives quality |
| **Share experience** | Write about using a security tool in production (bypasses, false positives, tuning) | Adds real-world context |
| **Improve methodology** | Propose a better adversarial benchmark, scoring rubric, or compliance mapping | Improves fairness |
| **Build an adapter** | Implement the security adapter for a new tool | Enables testing |
| **Report a bypass** | Share a responsibly disclosed bypass technique | Improves the ecosystem |
| **Review an edition** | Proofread, fact-check, suggest improvements | Improves quality |
| **Spread the word** | Share the almanac with your security community | Grows the ecosystem |

## Adding a New Security Tool

### Before you submit

Check if the tool meets the triage criteria:

1. **Seriousness**: Is it a real security tool with real users, not a toy or demo? Does it actually perform a security function (input validation, prompt injection defense, output filtering, PII detection, etc.)?
2. **Activity**: Has it had a push or release in the last 6 months?
3. **Documentation**: Does it have a README, docs, or landing page explaining what attacks it covers and how to configure it?
4. **Accessibility**: Is it testable (open source, free tier, or evaluation license available)? Security tools that require an enterprise-only sales process are excluded.
5. **Scope**: Does it fit the security category? A general-purpose AI platform with a minor security checkbox does not enter unless security is the primary focus.

### How to submit

**Option 1: GitHub Issue (preferred)**

File an issue with this template:

```markdown
## Tool Request: [Tool Name]

### Category
Security, Guardrails & AI Safety

### Tool URL
[GitHub repo or homepage URL]

### License
[e.g., MIT, Apache-2.0, Proprietary]

### Type
[e.g., Guardrail Framework, Prompt Injection Detector, PII Scrubber, Output Filter, Safety Aligner, Policy Engine]

### Description
[What does it do? One paragraph. What attacks does it defend against? How is it deployed?]

### Why it should be on the roster
[Evidence of adoption, production usage, compliance certifications, or technical merit.]

### Evidence
- GitHub stars: [N]
- Last release: [date]
- Notable users: [companies, if known]
- Compliance certifications: [NIST AI RMF, EU AI Act, SOC 2, etc.]
- Funding: [amount, if known]

### Tier suggestion
[A, B, or C — and why]
```

**Option 2: Pull Request**

If you want to add the tool directly:

1. Fork the repo
2. Edit `data/roster.json` to add the tool
3. Update `README.md` if the tool is Tier A
4. Submit a PR with the same template as above

### What happens after submission

1. **Triage**: We check if the tool meets criteria (within 7 days)
2. **Smoke gate**: We run the tool through the 3-turn security scenario (within 14 days): configure policy, send benign prompt, send adversarial prompt and verify block
3. **Decision**: Accepted, rejected, or deferred with a note
4. **Publication**: If accepted, it appears in the next edition
5. **Adapter**: If you submitted an adapter, we review it and merge it into the harness repo

## Fixing Data

### If you find incorrect metadata

File an issue with:

```markdown
## Data Correction: [Tool Name]

### Current (incorrect) data
[What does the roster say?]

### Correct data
[What should it say?]

### Evidence
[Link to the source that proves the correct data.]
```

### Common corrections

| Field | Common errors | How to verify |
|-------|--------------|---------------|
| License | Wrong SPDX identifier | Check the repo's LICENSE file |
| Stars | Out of date | Check the GitHub API |
| Last push | Wrong date | Check the GitHub repo |
| Tier | Wrong tier | Check the tier rules in IMPLEMENTATION.md |
| Notes | Outdated description | Check the tool's homepage/docs |
| Compliance | Missing or incorrect certifications | Check the tool's compliance page or SOC 2 report |
| Type | Wrong type | Check the tool's primary function (is it a guardrail, detector, scrubber, etc.?) |

### What happens after submission

Data corrections are reviewed and applied in the next edition cycle. We don't edit editions retroactively; we correct the data and note it in the next edition.

## Challenging a Ranking

### If you believe a score is wrong

File an issue with:

```markdown
## Challenge: [Tool Name] on [Dimension]

### Current score
[What does the almanac say?]

### Your evidence
[What data do you have?]

### What you did to verify
[Steps you took to reproduce or verify.]

### Suggested resolution
[What should change? Re-run? Different score? Methodology update?]
```

### What evidence is valid

| Evidence Type | Strength | Example |
|---------------|----------|---------|
| Raw results JSON analysis | Strong | "I re-analyzed the JSON and found that the adapter was configured with threshold=0.9 instead of the documented 0.7" |
| Independent reproduction | Strong | "I ran the harness with the same adapter and got a 15% higher detection rate" |
| Adapter bug report | Strong | "The adapter does not handle Base64-encoded prompts correctly, causing false negatives" |
| Bypass documentation | Strong | "I found a bypass that is not in the attack prompt library; here's the reproduction" |
| Vendor claim | Weak | "The vendor says Z" — but we already test vendor claims independently |
| Anecdote | Weak | "It worked for me" — not reproducible; we need structured data |

### What happens after submission

1. **Review**: We review the evidence (within 7 days)
2. **Reproduction**: If the claim is reproducible, we re-run the test
3. **Update**: If the re-run confirms the challenge, we update the score
4. **Publication**: The update appears in the next edition

## Improving the Methodology

### If you want to propose a methodology change

File an issue with:

```markdown
## Methodology Proposal: [Title]

### Current state
[What does the methodology say now?]

### Proposed change
[What should it say?]

### Rationale
[Why is this better? What problem does it solve?]

### Impact
[Which tools would be affected?]

### Backward compatibility
[Can old results be re-scored with the new method?]
```

### Methodology change process

1. **RFC**: The proposal is posted as an RFC for public comment (30 days)
2. **Discussion**: Community feedback is collected
3. **Decision**: ArdurAI makes the final decision based on feedback
4. **Announcement**: If accepted, a public announcement is made with a transition plan
5. **Implementation**: The change is implemented in the next edition cycle
6. **Re-run**: Affected benchmarks are re-run with the new methodology

### What kinds of changes are accepted

| Change Type | Likelihood | Example |
|-------------|------------|---------|
| Bug fix in harness or adapter | High | "The adapter incorrectly handles Unicode zero-width characters" |
| New adversarial benchmark | Medium | "Add a new benchmark for image-based prompt injection (multimodal attacks)" |
| New compliance standard | Medium | "Add ISO 42001 mapping to the compliance checklist" |
| Weight adjustment | Medium | "Increase accuracy weight from 30% to 35% because false negatives are critical in regulated industries" |
| New dimension | Low | "Add a 'explainability' dimension — can the tool explain why it blocked a prompt?" |
| Remove dimension | Very low | "Remove latency as a dimension" |

### What kinds of changes are rejected

- Changes that favor a specific vendor
- Changes that reduce reproducibility
- Changes that increase complexity without clear benefit
- Changes that are not backward-compatible without a migration plan
- Changes that weaken the adversarial testing (e.g., removing an attack category because it is "too hard")

## Reporting Bypasses

### If you discover a bypass in a tool on the roster

We follow a **90-day responsible disclosure** policy:

1. **Notify the vendor privately** via email or their security advisory channel. Include a clear reproduction.
2. **Wait 90 days** for the vendor to patch.
3. **If patched**: We re-test the tool and update the edition with the patched version.
4. **If not patched**: We publish the bypass in the edition and per-tool page after 90 days.
5. **If the vendor has no security contact**: Publish after 90 days regardless.

To report a bypass to the almanac:

```markdown
## Bypass Report: [Tool Name]

### Attack type
[e.g., Direct prompt injection, Indirect injection, Jailbreak, Encoding bypass, Multi-turn attack]

### Bypass description
[What does the attack do? How does it evade the tool?]

### Reproduction
[Exact prompt or code to reproduce the bypass.]

### Responsible disclosure date
[When did you notify the vendor?]

### Vendor response
[Has the vendor acknowledged? Patched?]

### Impact
[What can an attacker do if this bypass succeeds?]
```

### What happens after submission

1. **Verification**: We verify the bypass using the tool's adapter (within 7 days)
2. **Classification**: The bypass is classified in the failure mode taxonomy (see TESTING.md)
3. **Disclosure**: If within the 90-day window, we keep it private. If after 90 days, we publish it.
4. **Re-test**: We re-test the tool after the vendor patches (or after 90 days)
5. **Update**: The tool's score is updated in the next edition

## Code of Conduct

### Be respectful

This is a collaborative project. Treat others with respect, even when you disagree. Security is a contentious field; vendors may be frustrated by low scores. Stay factual and evidence-based.

### Be evidence-based

Claims should be backed by evidence. "I think X is better" is not enough. "I measured X and found Y" is. For bypass reports, provide exact reproduction steps. For challenges, provide raw data or independent reproduction.

### Be constructive

Criticism is welcome if it's constructive. "This is wrong" is not helpful. "This is wrong because the adapter was configured with threshold=0.9 instead of 0.7, and here's the evidence" is.

### Be responsible

Bypasses are serious. Report them responsibly. Do not use the almanac to attack systems without permission. Do not publish zero-days without following the 90-day policy.

### Be patient

The almanac is maintained by a small team. Responses may take time. Repeated pings are not helpful.

### No spam

Don't submit the same tool multiple times. Don't submit tools that clearly don't meet criteria. Don't use the almanac for marketing. Don't submit bypasses that are already publicly known (check the edition and per-tool pages first).

## License

By contributing to the almanac, you agree that your contributions are licensed under CC BY 4.0 for content and MIT for code.

## Attribution

Contributors are recognized in the edition notes. If you make a significant contribution (e.g., adding 5+ tools, fixing major data issues, improving methodology, discovering and responsibly disclosing bypasses), you will be listed as a contributor in the next edition.

## License

Content: CC BY 4.0  
Code: MIT
