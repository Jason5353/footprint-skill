# footprint

A [Claude](https://claude.com/claude-code) **skill** that turns Claude into a precise, security-minded personal digital-footprint auditor.

Give it a name (and ideally a town or an email), and it works outward through open sources — Companies House, GitHub, WHOIS, breach databases, social and developer platforms, and UK public records — to show you exactly what an attacker, stalker, or employer could learn about you. It maps the identity graph it uncovers, then produces a single cited HTML report with a prioritised remediation plan and a spear-phishing attack-surface analysis.

It was built to run **self-audits** and drive **security-awareness exercises** — the point is to see your own exposure the way an adversary would, and then fix it.

> ⚠️ **Passive OSINT only.** This skill searches and reads public sources. It performs **no active scanning**, collects **no credentials or passwords**, and is intended for auditing **yourself** or a subject who has **explicitly authorised** the exercise. Do not use it for doxxing, harassment, stalking, or profiling people without consent.

---

## What it looks for

| Area | Sources |
|------|---------|
| **Identity & location** | Companies House (name, DOB, home address, previous addresses), electoral roll, Land Registry |
| **Work & employment history** | LinkedIn, GitHub PR/commit history across employer orgs, company records |
| **Developer footprint** | GitHub (commit-email pivot to find hidden alias accounts), social graph, repo READMEs, Gists |
| **Cross-platform identity** | Keybase, Hacker News, Reddit, npm/PyPI, Mastodon — linking pseudonymous accounts |
| **Domain & infrastructure** | WHOIS (incl. pre-GDPR archives), hosting/VPS, Wayback Machine, reverse analytics |
| **Credential hygiene** | Have I Been Pwned and similar breach checks (email only — never passwords) |
| **UK public records** | Planning applications, Gazette, insolvency register, professional registers, charity trustees |
| **Family / co-resident exposure** | Partners and family exposed via the same records |
| **Attack surface** | Spear-phishing vectors built from every finding, with remediation |

## Key techniques

The skill encodes a repeatable **developer pivot chain** that reconstructs a full picture from a single email address:

```
email → GitHub commit-email pivot → real/alias account → social graph
      → PR history (employment timeline) → LinkedIn confirmation
      → Keybase / HN about-field → confirmed Reddit + identity graph
      → connection graph → phishing attack-surface analysis
```

Plus UK-specific playbooks (Companies House filing history for address trails, IDOX planning portals) and operational notes on tooling and known blockers (e.g. Reddit being inaccessible to automated fetchers, using the HN Algolia API rather than site-search).

---

## Installation

This is a standard Claude Code skill: a folder containing a `SKILL.md`.

**Personal (available in every project):**

```bash
git clone https://github.com/elbeanio/footprint-skill.git
cp -R footprint-skill/footprint ~/.claude/skills/footprint
```

**Project-scoped (available in one repo):**

```bash
cp -R footprint-skill/footprint /path/to/your/project/.claude/skills/footprint
```

Then start Claude Code and the `footprint` skill will be available.

## Usage

Invoke the skill and give it a subject:

```
/footprint audit me — Jane Doe, Bristol, jane@example.com
```

or just:

```
/footprint search for me
```

Claude will:

1. Confirm no secrets will be stored, and anchor the subject's identity so it audits the right person.
2. Run the source checks in parallel (and, for technical subjects, the developer pivot chain).
3. Produce a single styled HTML report saved to the working directory, including an interactive connection graph.
4. Offer to drill deeper on any area.

### Example prompts

- `/footprint audit John Smith in Manchester — threat model: targeted attacker`
- `/footprint here's my email, find everything linked to it: me@mydomain.com`
- `/footprint I run a Ltd company — check what's exposed via Companies House and fix it`

### What you get back

A self-contained `footprint-report-<subject>.html` file with:

- An overall risk banner (HIGH / MEDIUM / LOW)
- Per-category findings, each with the exact data found and a source link
- An interactive identity/connection graph
- A spear-phishing attack-vector section (why each finding matters)
- A prioritised action plan (24 hours / this week / this month)
- An ongoing-monitoring checklist and full source list

---

## Notes & limitations

- **UK-weighted.** The public-records playbooks (Companies House, Gazette, IDOX planning, electoral roll) are UK-specific. The developer, domain, breach, and social-platform techniques are global.
- **Some sources need a real browser.** JavaScript-rendered portals (e.g. IDOX planning) and Reddit are flagged as "manual check required" rather than fetched automatically.
- **`gh` CLI** should be authenticated for the GitHub pivots (`gh auth status`).
- **No paid lookups are performed automatically.** Where a paid service (e.g. historical WHOIS, PimEyes) would go deeper, the skill notes it rather than spending money.

## Ethics & scope

Only audit **yourself**, or someone who has **explicitly consented**. This tool exists so people can find and close their own exposure before an attacker exploits it. It refuses to assist with doxxing, harassment, or profiling non-consenting individuals, and it never asks for or stores passwords, API keys, or other secrets.

## License

MIT — see [LICENSE](LICENSE).
