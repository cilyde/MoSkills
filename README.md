# MoSkills

A curated collection of production-grade cognitive protocols, architectural enforcement tools, and adversarial verification skills for autonomous AI coding agents (Google Antigravity, Claude, and modern agentic IDEs).

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)
[![Skills](https://img.shields.io/badge/Skills-6%20Available-brightgreen.svg)](#included-skills)
[![Standard](https://img.shields.io/badge/Format-Agent%20SKILL.md-orange.svg)](#installation--usage)

---

## 🎯 Overview

AI coding agents often fall victim to predictable cognitive failure modes:
- **Sycophancy & Confirmation Bias**: Agreeing with flawed user plans or mistaking stack trace crash sites for true root causes.
- **Reflexive Contrarianism**: Inventing hypothetical bugs and far-fetched edge cases with zero codebase evidence.
- **Premature Over-Engineering**: Creating unnecessary abstractions, factories, and utility wrappers when native one-liners or existing code suffice.
- **Wheel Reinvention & Layer Leaks**: Rewriting domain logic in UI widgets or controllers instead of reusing canonical services.
- **Shallow Execution**: Silent multi-file edits without transparent reasoning or rigorous reconnaissance.

**MoSkills** provides battle-tested cognitive and operational protocols packaged as standard `SKILL.md` definitions. They enforce strict behavioral guardrails, adversarial debate, binary go/no-go verdicts, and visible chain-of-thought execution.

---

## 🧩 Included Skills

| Skill | Focus Area | Activation | Key Invariant / Mechanism |
| :--- | :--- | :--- | :--- |
| [**`fact-check`**](./fact-check/SKILL.md) | Adversarial Verification | Explicit User Request (`/fact-check`) | Dual-subagent debate (Advocate vs. Skeptic) with mandatory binary verdict & Concession Clause. |
| [**`opus-thinking-protocol`**](./opus-thinking-protocol/SKILL.md) | Transparent Reasoning | Auto or Multi-File / Complex Tasks | Dense visible narration out loud between tool calls to prevent shallow trajectory collapse. |
| [**`push-bouncer`**](./push-bouncer/SKILL.md) | Pre-Push Security Audit | Pre-Push / Commits (`/push-bouncer`) | The 75% Confidence Rule with zero-tolerance binary `PUSH VERDICT: [YES \| NO]` gate. |
| [**`special-forces-unit`**](./special-forces-unit/SKILL.md) | Architectural Enforcement | Audits, Refactors, PR Reviews | Sector-based codebase sweeps hunting logic reinvention, layer boundary leaks, and code sprawl. |
| [**`un-complicator`**](./un-complicator/SKILL.md) | Simplicity & YAGNI | Code Review / Pre-Implementation | The 6-Rung Simplification Ladder & 10-point simplicity scoring rubric. |
| [**`un-tunnelvision`**](./un-tunnelvision/SKILL.md) | Root Cause Debugging | Bug Investigation & Test Failures | Binary `ROOT CAUSE CONFIRMED: YES/NO` upfront; separates origin from detonation site. |

---

## 🔍 Deep Dive: The Skills

### 1. [`fact-check`](./fact-check/SKILL.md)
> *Rigorous adversarial fact-checking with dual-agent debate and binary verdicts.*

- **Trigger**: Strictly user-requested (`/fact-check`, "fact check this"). Agents are strictly prohibited from activating this autonomously.
- **Protocol**:
  1. Deploys two concurrent, read-only subagents: **Advocate** (finds verifiable code evidence supporting the claim) and **Skeptic** (finds verifiable code contradictions).
  2. **The Concession Clause**: Both agents are mandated to concede when no factual basis exists for their assigned stance (eliminates fake counter-arguments and reflexive skepticism).
  3. **Mandatory Binary Verdict**: Concludes with an unambiguous `FACT CHECK VERDICT: VERIFIED (YES)` or `FACT CHECK VERDICT: REFUTED (NO)`.

---

### 2. [`opus-thinking-protocol`](./opus-thinking-protocol/SKILL.md)
> *Forces frontier extended-thinking rigor and visible reasoning from Turn 1.*

- **Core Rule**: **Narrate out loud in visible response text, not hidden in internal thinking.**
- **Why it works**: By narrating findings, surprises, and decisions between tool calls, subsequent conversation turns inherit analytical depth rather than collapsing into shallow execution.
- **Workflow**:
  1. **Deconstruct**: Quote exact user constraints and identify boundary conditions / negative exceptions.
  2. **Investigate**: Search before acting; narrate findings between every tool call.
  3. **Plan Visibly**: Declare file edit scopes and anticipated ripple effects.
  4. **Execute & Verify**: Verify syntax, contracts, and tests explicitly before closing.

---

### 3. [`push-bouncer`](./push-bouncer/SKILL.md)
> *Pre-push security bouncer enforcing a binary gate against credential leaks and exploitable flaws.*

- **Trigger**: Pre-push verification (`/push-bouncer`, before `git push`, or when staging commits).
- **Core Principle**: If there is a $\ge 75\%$ probability that code contains an easily exploitable flaw, hardcoded credential, or accidentally bundled secret, the bouncer turns you away: verdict is strictly **NO**.
- **The Binary Push Gate**: Concludes strictly with `PUSH VERDICT: YES` or `PUSH VERDICT: NO` (zero conditional approvals allowed).
- **Four Audit Pillars**:
  1. **High-Entropy Secrets & Credentials**: API keys, bot tokens, database passwords, private keys/certs.
  2. **Accidental File Bundling**: `.env`, keystores, SQLite databases, cloud credentials.
  3. **Privilege Escalation & Auth Bypasses**: `isAdmin = true`, commented-out auth guards, backdoor query params.
  4. **Easily Mis-used Security Flaws**: Plaintext logging of passwords/tokens, raw SQL concatenation, open CORS with credentials.

---

### 4. [`special-forces-unit` (SFU)](./special-forces-unit/SKILL.md)
> *Elite architectural enforcement hunting logic reinvention and layer violations.*

- **Mission**: Intercept code written from scratch where canonical domain functions already exist, and prevent domain logic from leaking into presentation layers.
- **Features**:
  - **Tactical Isolation**: Runs in strict isolation from conflicting workflow skills.
  - **Sector Decomposition**: Splits large codebases into non-overlapping sectors (UI, Controllers, Repositories/Services, Integrations) and dispatches parallel reconnaissance subagents.
  - **Actionable Reporting**: Produces structured `CommandCentre.md` audits and `SIREN.md` alert feeds.

---

### 5. [`un-complicator`](./un-complicator/SKILL.md)
> *Anti-over-engineering protocol: "Stop at the first rung that holds."*

- **The 6-Rung Simplification Ladder**:
  1. **Rung 1 (YAGNI)**: Does this need to exist? If speculative, drop it.
  2. **Rung 2 (Codebase Reuse)**: Already in this codebase? Reuse canonical source.
  3. **Rung 3 (Native Platform)**: Built into the language/standard library? Use it.
  4. **Rung 4 (Installed Dependency)**: Already in manifest (`pubspec.yaml`, `package.json`, etc.)? Use it.
  5. **Rung 5 (One-Liner)**: Can it be a clean, readable one-liner? Don't wrap it in a service class.
  6. **Rung 6 (Minimum Viable)**: Only then write the leanest viable implementation.
- Includes a **10-Point Simplicity Scorecard** with concrete deduction penalties for speculative abstractions and indirection noise.

---

### 6. [`un-tunnelvision`](./un-tunnelvision/SKILL.md)
> *Unbiased root cause analysis for bugs, test failures, and system regressions.*

- **The Iron Law**: State `ROOT CAUSE CONFIRMED: YES` or `ROOT CAUSE CONFIRMED: NO` as the very first line before providing any justification.
- **Core Checks**:
  - **Origin vs. Detonation Site**: Distinguish where invalid state was created from where execution crashed.
  - **Explanatory Completeness**: The root cause must account for 100% of symptoms without dismissing concurrent logs as "noise".
  - **Contradictory Evidence**: If no contradictions exist and mechanism is sound, confirm immediately; do not invent phantom bugs.

---

## 🚀 Installation & Usage

### Installing in Google Antigravity
You can install these skills globally or for a specific workspace:

```bash
# Global installation (available across all projects)
mkdir -p ~/.gemini/antigravity/skills
cp -r /path/to/MoSkills/* ~/.gemini/antigravity/skills/

# Or workspace-specific installation
mkdir -p .agent/skills
cp -r /path/to/MoSkills/* .agent/skills/
```

### Installing in Claude Code / Cursor / Windsurf
Copy individual skill directories into your agent's custom rules or skills configuration directory:

```bash
# For Claude Code / custom agent directories
cp -r fact-check ~/.claude/skills/
# Or copy relevant instructions into your project's .cursorrules / .windsurfrules
```

---

## 🏛️ Core Design Principles

1. **Grounded in Code Reality**: Zero speculation. Every claim or objection must cite concrete file paths, line numbers, or test outputs.
2. **Binary Verdicts Before Nuance**: Real engineering requires unambiguous go/no-go decisions. Verdicts lead; nuance follows.
3. **No Unearned Skepticism, No Sycophancy**: Objectivity cuts both ways. The Concession Clause ensures agents agree when code is right and refute when code is broken.
4. **Lean Over Clever**: The best code is no code; the second best is the simplest code that solves today's verified problem.

---

## 📄 License

This repository is licensed under the [MIT License](LICENSE).
