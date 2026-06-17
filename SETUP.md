# Setup Guide

How to clone, contribute to, and push changes to the Security, Guardrails & AI Safety Almanac.

## Prerequisites

- Git 2.40+
- A GitHub account
- (Optional) Python 3.11+ if running the benchmark harness locally

## Clone the repo

```bash
git clone https://github.com/ArdurAI/ai-security-almanac.git
cd ai-security-almanac
```

## Repository structure at a glance

```
ai-security-almanac/
├── README.md                          # Project overview + roster at a glance
├── INTENT.md                          # Philosophy, design principles, governance
├── IMPLEMENTATION.md                  # How to add tools, update editions, data pipeline
├── TESTING.md                         # Benchmark methodology, harness details
├── TROUBLESHOOTING.md                 # Common issues, debugging, FAQ
├── CONTRIBUTING.md                    # How to contribute
├── architecture.md                    # Stack architecture + test philosophy
├── SETUP.md                           # This file
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
├── tools/                             # Per-tool deep-dive pages
│   └── (populated as deep-dives are written)
│
└── assets/                            # Charts, diagrams, screenshots
    └── (populated by editions)
```

## Making changes

### 1. Create a branch

```bash
git checkout -b your-branch-name
```

### 2. Edit files

All content is Markdown. Edit in your preferred editor.

Key files to edit for common tasks:

| Task | File(s) |
|------|---------|
| Add a new tool | `data/roster.json`, then `tools/<name>.md`, then `editions/YYYY-MM.md` |
| Update the monthly edition | `editions/YYYY-MM.md` |
| Add benchmark results | `benchmarks/security-<suite>-<date>.json` + `.md` |
| Update methodology | `methodology/benchmark-harness.md` (requires RFC) |
| Fix a typo | Any `.md` file |

### 3. Validate your changes

#### JSON validation

If you edited `data/roster.json`, validate it:

```bash
python3 -c "import json; json.load(open('data/roster.json'))" && echo 'JSON valid'
```

#### Markdown lint (optional)

```bash
# If you have markdownlint
markdownlint **/*.md --ignore node_modules
```

#### Check for dead links (optional)

```bash
# If you have lychee
lychee **/*.md
```

### 4. Commit

Follow the commit message conventions:

```
<edition|tool|benchmark|docs|fix>: <short description>

[optional body]
```

Examples:

```
edition: 2026-06 findings — add Tier B roster, attack patterns, quest diary
tool: add Guardrails AI deep-dive page
benchmark: add OWASP LLM Top 10 results for 12 Tier A tools
fix: correct Prompt Security acquisition date in roster
docs: update IMPLEMENTATION.md with new adapter pattern
```

### 5. Push and open a PR

```bash
git push origin your-branch-name
```

Open a pull request on GitHub. PRs should:

- Reference the edition or tool being updated
- Include a summary of what changed and why
- Pass JSON validation if `roster.json` was edited
- Be reviewed by at least one maintainer

## For maintainers: Publishing a new edition

1. **Create the edition file**: `editions/YYYY-MM.md` from the template in `IMPLEMENTATION.md`
2. **Update `roster.json`**: Refresh metadata, add/remove tools, update tiers
3. **Update `README.md`**: Refresh the roster-at-a-glance table
4. **Commit**: `edition: YYYY-MM — [theme]`
5. **Tag**: `git tag -a vYYYY-MM -m "Edition YYYY-MM" && git push origin vYYYY-MM`
6. **Announce**: Post summary to relevant channels (see `CONTRIBUTING.md`)

## GitHub Actions

The repo includes optional GitHub Actions for:
- **Monthly metadata refresh**: Runs on the 1st of each month to refresh GitHub stars, last push dates, and compliance certifications
- **Link checking**: Validates all external links in the repo
- **JSON validation**: Ensures `roster.json` is always valid

See `.github/workflows/` for configuration.

## License

Content: CC BY 4.0  
Code: MIT

By contributing, you agree that your contributions will be licensed under these terms.
