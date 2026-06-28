# Australian Legal Research — a Claude Code Skill

A multi-agent [Claude Code](https://claude.com/claude-code) **skill** for Australian legal research and citation verification. It retrieves Australian primary law from AustLII, CaseLaw NSW, and the legislation registers, then runs an orchestrated, self-validating research pass over a legal question — never citing a case, quote, or statutory provision from memory.

> ⚠️ **Not legal advice.** This skill is a research aid. It surfaces and verifies authorities; it does not give legal advice. Always confirm results against the primary source before relying on them.

## What it does

The skill does two things:

- **Retrieves** Australian primary law — case law, legislation, and topical searches — from [AustLII](https://www.austlii.edu.au), [CaseLaw NSW](https://www.caselaw.nsw.gov.au), the [Federal Register of Legislation](https://www.legislation.gov.au), and [NSW legislation](https://legislation.nsw.gov.au).
- **Researches** — runs an orchestrated, multi-agent pass over a legal question and **self-validates every authority** (existence, pinpoint, quote, treatment, red-team) before reporting it.

### Architecture: orchestration → diverge-converge

1. **Stage 1 — Orchestration.** The orchestrator decomposes the task into a research goal and the subgoals each agent must fulfil (jurisdiction, temporal anchor, factual matrix, propositions).
2. **Stage 2 — Diverge-converge.** It dispatches a fixed roster of parallel agents — legislation, case law, evidence, jurisdiction/court-rules, secondary, and red-team — converges and reconciles their reports, runs a five-check citation-integrity gate, and synthesises a memo.

The decomposition scales to the task: a single citation lookup is a trivial one-subgoal pass; a full research question engages the entire roster.

### The verification principle

> Never cite an Australian case, quote a judgment, reproduce statutory text, or state case metadata from memory alone. Always verify by fetching the source.

Verification produces three states only — **verified, suspect, or overridden** — never "probably fine".

## Optional: the `jurisd` MCP server

The skill ships no MCP server but **prefers one if present**. When the optional `jurisd` MCP server is registered, its tools are the preferred (Layer-0) retrieval path — authority-ranked AustLII search, clean full-text fetch, deterministic provision lookup, an offline citation-graph tool, and AGLC4 formatting. Without it, the skill drives the runtime's own primitives (`WebFetch`, web search, Claude in Chrome). Either way the skill works — `jurisd` is preferred, never required.

## Installation

Clone this repo into your Claude Code skills directory so the skill folder sits alongside your other skills:

```bash
# Personal (all projects)
git clone https://github.com/acor8826/claude-australian-legal-research.git \
  ~/.claude/skills/australian-legal-research-tmp
mv ~/.claude/skills/australian-legal-research-tmp/australian-legal-research \
  ~/.claude/skills/australian-legal-research
rm -rf ~/.claude/skills/australian-legal-research-tmp
```

Or, for a single project, copy the `australian-legal-research/` folder into that project's `.claude/skills/` directory.

Claude Code auto-discovers any folder containing a `SKILL.md`, so once `australian-legal-research/SKILL.md` is in a skills directory it is available.

## Usage

Once installed, the skill triggers automatically on Australian legal research requests, or you can invoke it explicitly. Triggers include:

- "find / verify a citation", e.g. *"is `[2019] FCA 1234` real and still good law?"*
- "fetch an Act / section", e.g. *"pull s 41 of the Bankruptcy Act 1966"*
- "what's the law on X", "find authorities for …", "find cases with similar facts"
- any mention of AustLII or an Australian case citation

**Use it before relying on training knowledge for any Australian citation, quote, statutory text, judge, date, or holding.**

## Repository layout

```
australian-legal-research/
├── SKILL.md                       # Skill definition + orchestration architecture
└── references/
    ├── retrieval-mechanics.md     # Layer-0 precedence, URL patterns, fetch recovery
    ├── agent-legislation.md       # Per-area agent briefs …
    ├── agent-caselaw.md
    ├── agent-evidence.md
    ├── agent-jurisdiction.md
    ├── agent-secondary.md
    ├── agent-redteam.md
    ├── agent-validation.md        # The five-check citation-integrity gate
    └── templates.md               # Subagent prompt + memo templates
```

## License

[MIT](./LICENSE)
