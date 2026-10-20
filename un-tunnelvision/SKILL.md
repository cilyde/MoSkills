---
name: un-tunnelvision
description: Use when a suspected root cause has been identified for a bug, test failure, crash, or unexpected system behavior, before proposing solutions, writing implementation plans, or applying fixes
---

# Un-TunnelVision

## Overview

The moment a root cause is proposed, objective evaluation begins.

**Core principle:** True objectivity cuts both ways. Never accept a hypothesis out of confirmation bias, and never oppose a hypothesis out of reflexive contrarianism. Evaluate strictly against hard evidence without bias.

## The Iron Law

```
EVERY ROOT CAUSE EVALUATION MUST STATE A BINARY "YES" OR "NO" BEFORE JUSTIFICATION
```

The very first line of your evaluation MUST be:
- `ROOT CAUSE CONFIRMED: YES`
- OR `ROOT CAUSE CONFIRMED: NO`

**No preamble, no equivocation, no fence-sitting.** State the binary verdict first, then provide the justification.

## Two Opposite Traps of Tunnel Vision

Tunnel vision has two equally destructive faces:

1. **Confirmation Bias (Gullible Tunnel Vision):**
   Anchoring on the first plausible explanation, mistaking the crash site for the origin, or hand-waving contradictory logs as "noise".
2. **Reflexive Contrarianism (Cynical Tunnel Vision):**
   Opposing the given view without factual basis, inventing far-fetched hypothetical failure modes, or refusing to accept an accurate root cause just to appear "skeptical".

**IT MUST BE UNBIASED:**
- If the identified root cause is backed by concrete facts, explains all symptoms, and has no contradictory evidence, **you MUST confirm it with YES**. Opposing without basis is forbidden.
- If the identified root cause is contradicted by evidence, mistakes a detonation site/symptom for the origin, or fails to explain other concurrent symptoms, **you MUST reject it with NO**.

## The Unbiased Protocol

Every evaluation must follow this exact two-step format:

### Step 1: Binary Verdict
```markdown
ROOT CAUSE CONFIRMED: [YES | NO]
```
- **YES:** The evidence conclusively proves this is the true origin, explains all observed symptoms, and no contradictory facts exist.
- **NO:** The evidence disproves this hypothesis, reveals it is only a symptom/detonation site, or leaves critical observations unexplained.

### Step 2: Unbiased Evidence Justification
Evaluate strictly against these 4 objective criteria:

1. **Origin vs. Detonation Site:**
   - Did the bad state originate here, or did it originate upstream and merely explode here?
   - If this is the origin, state it. If it is only the crash site, identify the true origin.
2. **Explanatory Completeness:**
   - Does this explanation account for **100%** of the observed symptoms, logs, and timing?
   - Does it require dismissing concurrent errors or logs as "unrelated"?
3. **Contradictory Evidence:**
   - Is there any log, test result, timestamp, or invariant that contradicts this theory?
   - **Crucial Rule:** If NO contradictory evidence exists and the mechanism is sound, **do NOT invent objections**. Confirm with YES.
4. **Deterministic Mechanism:**
   - Is there an unbroken causal chain from trigger to failure?
   - Does eliminating this origin deterministically resolve the failure?

## Red Flags — Signs of Biased Thinking

### Confirmation Bias Red Flags:
- "The stack trace points here, so this must be the root cause."
- "Let's just add a null-check / fallback and see if it passes."
- "Those other error logs around the same time are just noise."

### Contrarian Bias Red Flags:
- "It couldn't be that simple; there must be a deeper mystery." (rejecting a proven bug without evidence)
- Opposing the user's or previous agent's view without citing a specific contradictory log or code line.
- Demanding impossible proofs or inventing hypothetical flaws that have no basis in the codebase.

## Rationalization Prevention

| Excuse | Reality |
|---|---|
| "The stack trace points right here" | Stack traces show where execution crashed, not where state was corrupted. |
| "A quick null-check / fallback fixes it" | Silencing alarms causes downstream corruption. Find the true source. |
| "It can't be that simple, let's reject it" | If evidence cleanly proves it, accept it. Skepticism without evidence is bias. |
| "I'll oppose the view just to be safe" | False negatives waste hours just like false positives. Be strictly objective. |
| "Those concurrent errors are unrelated" | Correlate all logs before deciding. Dismissing clues is confirmation bias. |

## Quick Checklist

Before proposing or coding any fix:
- [ ] Stated binary `ROOT CAUSE CONFIRMED: YES` or `ROOT CAUSE CONFIRMED: NO` as the first line
- [ ] Evaluated evidence without confirmation bias (not mistaking symptom for cause)
- [ ] Evaluated evidence without contrarian bias (not opposing without concrete proof)
- [ ] Checked whether the code location is the origin or just the detonation site
- [ ] Verified that it explains all symptoms, or proved specifically why it fails to do so
- [ ] Cited concrete code lines, timestamps, or log entries (zero gut feelings or assumptions)
