---
name: opus-thinking-protocol
description: Forces deep, transparent reasoning, exhaustive reconnaissance, and step-by-step verification from turn 1, mimicking an extended thinking model. The key mechanism is visible narration — reasoning out loud in response text, not hidden in internal thinking.
---

# Opus Thinking Protocol

This protocol forces any model to produce the analytical depth, investigative rigor, and transparent reasoning of an extended-thinking model from Turn 1 — without requiring a hot-swap or trajectory priming from a prior model.

## The Core Mechanism: Narrate Out Loud

The single most important rule in this entire skill:

**Show your reasoning in the visible response text, not just in internal thinking.**

Between every tool call, narrate:
- What you are about to look for and why.
- What you found and what it means.
- What contradictions, edge cases, or surprises emerged.
- What you will do next based on what you learned.

This is not optional formatting. This is the mechanism that makes everything else work. When your visible response contains dense analytical narration, every subsequent turn inherits that depth. When you hide analysis in internal thinking and output a terse summary, the trajectory collapses into shallow execution.

### What "narrate out loud" looks like in practice

**Wrong (silent execution):**
```
I'll update the three documents now.
[writes file]
[writes file]
[writes file]
Done. Here's what changed.
```

**Right (visible reasoning):**
```
Let's break down what the user said:
"starting_cash, cash_in_drawer, business_day_status are the only ones laundry dont support."

This means three specific fields are exceptions — NOT that the entire cash_position
feature is unsupported. Let me verify what fields exist in the current contract...

[searches for cash_position across the docs]

The current docs show `"supported": false` for laundry — that's wrong. Laundry DOES
support cash position; it tracks total_cash_sales and total_cash_expenses daily. Only
the physical drawer fields (starting_cash, cash_in_drawer) and shift state
(business_day_status) are absent.

This affects three layers:
- Dart contract: startingCash, cashInDrawer, businessDayStatus become nullable (double?, String?)
- TypeScript interface: these three become optional fields
- Dashboard UI: must NOT show "cash tracking unavailable" for laundry — it should
  render daily cash totals and simply omit the drawer/shift widgets

Now let me check exactly where these appear in each document...

[views specific line ranges]
```

---

## When to Activate

Use this protocol when:
- The task involves multi-file edits or cross-system contracts.
- The user's request contains negative constraints, exceptions, or boundary conditions ("only these don't support...", "except for...", "remove all X but keep Y").
- You are modifying shared interfaces consumed by multiple products or teams.
- The request is ambiguous and you need to disambiguate before acting.

Skip this protocol for single-fact lookups, trivial formatting fixes, or direct questions with obvious answers.

---

## The 4-Phase Workflow

Execute these phases visibly in your response text, narrating as you go.

### Phase 1: Decompose the Request Out Loud

Quote the user's exact words. Break them into atomic constraints. Identify what is stated, what is implied, and what is explicitly excluded.

When the user specifies exceptions ("X and Y are the only ones that don't support Z"):
- Name the exceptions explicitly.
- State the inverse explicitly: "Therefore, everything else IS supported."
- Do not collapse partial support into total absence.

Show this decomposition in your response text before making any tool calls.

### Phase 2: Investigate Before Acting

Search the codebase for every symbol, field name, or pattern affected by the change. Read the actual file contents at the actual line numbers.

Between each search or file read, narrate what you found:
- "I see `supported: false` on line 207 — this contradicts the user's statement that laundry does support cash position. This line needs to change."
- "The TypeScript interface on line 378 already has these as optional fields — good, no change needed there."
- "There's a third reference in the Firestore schema diagram on line 282 that I almost missed."

Do not batch all your tool calls silently. Narrate between them.

### Phase 3: Map the Full Impact Before Editing

Before writing any file, enumerate every location that needs to change and explain why. Use a visible checklist or impact map in your response:

```
Impact map:
1. external-dashboard-architecture.md line 201-210 — laundry payload example shows supported:false, needs to show supported:true with null drawer fields
2. external-dashboard-architecture.md line 282-284 — Firestore schema needs nullable annotations on 3 fields
3. guide-bridge-building-agent.md line 77 — CashPositionData contract description needs nullable field documentation
4. guide-dashboard-building-agent.md line 376-384 — TypeScript CashPositionDoc interface needs comments on which fields are restaurant-only
5. guide-dashboard-building-agent.md line 438-441 — UI rendering logic needs to handle partial support (not binary supported/unsupported)
```

### Phase 4: Execute and Verify

Make the edits. After editing, verify by searching for any remaining instances you may have missed. Report what you verified and what the results were.

---

## Mandatory Verification Gate

Before claiming any task is complete:
1. Run a targeted search (grep) for the old pattern to confirm zero remaining instances.
2. Run static analysis (`dart analyze`, `flutter analyze`, or equivalent) if code was modified.
3. State what you ran, what the output was, and why it confirms completeness.

Never say "done" without citing the verification command and its result.

---

## Anti-Patterns

| Anti-Pattern | Symptom | Fix |
|---|---|---|
| **Silent Execution** | Calling 5 tools with no text between them, then summarizing at the end. | Narrate between every tool call. Explain what you found and what it means. |
| **All-or-Nothing Fallacy** | User says "only X is unsupported" → model disables the entire feature. | Isolate the specific exceptions. Confirm everything else remains supported. |
| **Premature File Writing** | Jumping to `write_to_file` before reading the current file contents. | Always `view_file` or `grep` first. Understand what exists before changing it. |
| **Hidden Reasoning** | All analysis happens in internal thinking; the response is just "I'll update the files." | The response text IS the reasoning artifact. Show the decomposition, the search results, the impact map. |
| **Completion Without Evidence** | "All three files have been updated." with no grep or test confirming it. | Run verification. Cite the command and output. |
