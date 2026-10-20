---
name: un-complicator
description: Use when evaluating proposed or implemented code for over-engineering, unnecessary complexity, premature abstraction, or wheel-reinvention before writing code or during code review
---

# Un-Complicator

## Overview

Complexity is technical debt written today that collects interest tomorrow.

**Core principle:** The best code is no code. The second best code is the simplest code that solves today's actual problem.

## The Iron Law

```
STOP AT THE FIRST RUNG THAT HOLDS. NEVER CLIMB LOWER UNLESS THE HIGHER RUNG FAILS.
```

Before writing any new class, function, or abstraction, stop at the highest possible rung:

```
[Rung 1] Does this need to exist?   ──(No)──> Skip it (YAGNI). Delete/omit.
     │ (Yes)
[Rung 2] Already in this codebase?  ──(Yes)─> Reuse it. Do not rewrite.
     │ (No)
[Rung 3] Native platform feature?   ──(Yes)─> Use built-in language/runtime API.
     │ (No)
[Rung 4] Installed dependency?      ──(Yes)─> Use existing package in manifest.
     │ (No)
[Rung 5] Can it be one line?        ──(Yes)─> Write one clean, readable line.
     │ (No)
[Rung 6] Only then: the minimum that works. Leanest viable implementation.
```

---

## The 6-Rung Simplification Ladder

### Rung 1: Does this need to exist? (YAGNI)
- **Question:** Is this solving an immediate, verified requirement right now?
- **Action:** If it's for "future extensibility," "Q4 plugins," or a hypothetical edge case that has not occurred: **drop it**.
- **Stop here:** Code avoided has 0 bugs, 0 maintenance cost, and 0 execution time.

### Rung 2: Already in this codebase?
- **Question:** Has someone already written a helper, repository method, utility, or widget for this?
- **Action:** Search before coding (`grep_search` / `find_by_name`). Reuse the canonical source.
- **Stop here:** Do not create shadow helpers or duplicate utility functions.

### Rung 3: Native platform feature?
- **Question:** Does the language, framework, or standard library already do this?
- **Action:** Use core runtime methods (e.g., standard collections, `DateTime.utc`, regex, built-in layout widgets).
- **Stop here:** Never hand-roll custom algorithms for problems the standard library already solved.

### Rung 4: Installed dependency?
- **Question:** Is there already a package in `pubspec.yaml`, `package.json`, or `Cargo.toml` that solves this?
- **Action:** Import the installed dependency.
- **Stop here:** Do not reinvent date formatting if `intl` is present; do not build a custom HTTP retry loop if `dio` is installed.

### Rung 5: Can it be one line?
- **Question:** Can this be expressed as a standard ternary, stream/collection transform (`map`/`where`), or standard getter?
- **Action:** Write the single expressive line.
- **Stop here:** Do not wrap a single-line standard call in an ad-hoc 15-line service class.

### Rung 6: Only then — the minimum that works
- **Question:** If Rungs 1–5 failed, what is the absolute smallest, clearest implementation?
- **Action:** Write pure, straightforward functions. No premature design patterns, no speculative interfaces, no abstract factories.

---

## Simplicity Rating (Score Out of 10)

Score all proposed or reviewed solutions on a scale of **1 to 10**:

| Score | Tier | Description | Typical Rung |
|---|---|---|---|
| **10 / 10** | **Pure Elegance** | Zero excess code. Solved via YAGNI omission, existing codebase reuse, or native 1-liner. | Rungs 1–4 |
| **8 – 9 / 10** | **Lean & Pragmatic** | Minimal viable code. Stopped at Rung 5–6 with zero speculative abstractions. | Rungs 5–6 |
| **6 – 7 / 10** | **Mildly Complicated** | Slight over-engineering (e.g., trivial helper wrapping a 1-liner, minor duplicate utility). | Leaked past Rungs 3–5 |
| **4 – 5 / 10** | **Substantially Over-Engineered** | Premature patterns (unneeded interfaces, strategy patterns, bespoke parsers for standard data). | Leaked past Rungs 2–4 |
| **2 – 3 / 10** | **Severe Reinvention** | Hand-rolled platform features (custom date math, bespoke crypto, custom query engines). | Leaked past Rungs 3–4 |
| **1 / 10** | **Rube Goldberg Machine** | Multi-class speculative architecture solving imaginary future problems while adding maintenance burden. | Violates Rung 1 (Anti-YAGNI) |

### Deduction Guide (Start at 10)
- **-4 pts:** Violating Rung 1 (Building for speculative future requirements / YAGNI).
- **-3 pts:** Violating Rung 2 (Reinventing logic already existing in the project).
- **-3 pts:** Violating Rung 3 (Hand-rolling native platform / standard library capabilities).
- **-2 pts:** Violating Rung 4 (Re-implementing features of already-installed dependencies).
- **-2 pts:** Violating Rung 5 (Using a multi-line boilerplate class where a 1-line expression suffices).
- **-2 pts:** Speculative abstraction (creating abstract interfaces/factories with only one concrete implementation).
- **-1 pt:** Indirection noise (pass-through methods, redundant getters, unnecessary DTOs).

---

## Concrete Example: Before & After

### The Request:
> "Calculate the difference in calendar days between two dates, ignoring time of day."

```dart
// ❌ OVER-ENGINEERED (Score: 1/10) - Slipped to custom calendar engine
class DateDiffCalculator {
  static const daysInMonths = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
  static bool isLeapYear(int y) => y % 4 == 0 && (y % 100 != 0 || y % 400 == 0);
  int calculateDays(DateTime a, DateTime b) {
    // 60 lines of cumulative loops, year-0 leap year handling, and epoch math
  }
}

// ✅ UN-COMPLICATED (Score: 10/10) - Stopped at Rung 3 (Native Platform Feature)
int daysBetween(DateTime from, DateTime to) {
  return DateTime.utc(to.year, to.month, to.day)
      .difference(DateTime.utc(from.year, from.month, from.day))
      .inDays;
}
```

---

## Red Flags — STOP and Drop to a Higher Rung

- "We might need plugins or dynamic rules in Q3/Q4" → **Rung 1 (YAGNI)**
- "Let me write a quick helper function for date/string formatting" → **Rung 2 or 3**
- "Third-party libraries are bloat, I'll write my own parser" → **Rung 3 or 4**
- "Creating a Strategy Pattern and Factory makes this cleaner" → **Rung 6 (Minimum)**
- "A 1-liner is too simple; a class feels more professional" → **Rung 5 (One Line)**
- Abstract class with only one concrete implementation → **Over-engineering**
- Adding configurability for options nobody has requested → **YAGNI**

---

## Rationalization Prevention Table

| Excuse | Reality |
|---|---|
| "We'll need this extensibility later" | You do not know what future requirements look like. Premature abstractions lock you into the WRONG abstraction. |
| "Writing our own gives us 100% control" | 100% control means 100% ownership of edge cases, performance bugs, and maintenance debt. |
| "It's only 50 lines of code" | 50 lines of code still requires 50 lines of tests, mental overhead, and future maintenance. |
| "A 1-liner is too terse" | Idiomatic standard library expressions are understood instantly by all developers on the platform. |
| "I already wrote the class / designed the schema" | Sunk cost fallacy. Deleting unneeded code is an upgrade, not a waste. |
| "Clean architecture requires an interface here" | Clean architecture serves the software; software does not serve Clean Architecture dogma. |

---

## Review Checklist (The Un-Complicator Gate)

When reviewing your own proposed code or evaluating a PR:
- [ ] **Rung 1 Checked:** Does this solve a concrete requirement of today?
- [ ] **Rung 2 Checked:** Did I search the codebase for an existing solution?
- [ ] **Rung 3 Checked:** Does the language runtime or standard library already do this?
- [ ] **Rung 4 Checked:** Can an already-installed dependency solve this?
- [ ] **Rung 5 Checked:** Can this be expressed cleanly in 1 line?
- [ ] **Score Assigned:** Solution scored out of 10 with clear justification.
- [ ] **No Speculative Abstractions:** No single-impl interfaces, no uncalled factories.
