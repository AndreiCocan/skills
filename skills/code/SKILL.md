---
name: code
description: >
  Enforces the smallest change that fully solves the problem: YAGNI checks
  before writing, reuse of what the codebase already has, stdlib and native
  platform features before new dependencies, root cause over symptom on bug
  fixes. Use when you are writing, adding, refactoring, fixing, reviewing, or
  designing any code, and choosing libraries or dependencies.
---

# Code

Prefer the smallest change that fully solves the problem. Before writing
code, check whether it needs to exist and whether it already does.

Small is the *result* of understanding the problem, not a substitute for it.
A tiny diff in the wrong place is not restraint, it's a second bug.

## The ladder

Stop at the first rung that holds:

1. **Does this need to exist at all?** Speculative need = skip it. (YAGNI)
2. **Already in this codebase?** A helper, util, type, or pattern that already lives here → reuse it. Look before you write; re-implementing what's a few files over is the most common slop. But ask why it exists: most utils predate the stdlib gaining the feature rather than deliberately differing from it. Reuse it when it still adds behavior you actually want (your error wrapping, your context, your logging); when it's just an older version of what the stdlib now does, use the stdlib and note the helper as deletable.
3. **Stdlib does it?** Use it.
4. **Native platform feature covers it?** `<input type="date">` over a picker lib, CSS over JS, DB constraint over app code.
5. **Already-installed dependency solves it?** Use it. Never add a new one for what a few lines can do.
6. **Only then:** the minimum code that works.

The ladder is a reflex, not a research project. Two rungs both work → take
the lower-numbered one.

**Bug fix = root cause, not symptom.** A report names a symptom. Before you
edit, grep every caller of the function you're about to touch. The smallest
fix IS the root-cause fix: one guard in the shared function is a smaller diff
than a guard in every caller, and patching only the path the ticket names
leaves every sibling caller still broken. Fix it once, where all callers
route through.

## Rules

- No unrequested abstractions: no interface with one implementation, no factory for one product, no config for a value that never changes.
- No boilerplate, no scaffolding "for later", later can scaffold for itself.
- Deletion over addition. Boring over clever, clever is what someone decodes at 3am.
- Shortest working diff wins.
- Two stdlib options, same size? Take the one that's correct on edge cases. Writing less code never means picking the flimsier algorithm.
- Mark deliberate simplifications that cut a real corner with a known ceiling (global lock, O(n²) scan, naive heuristic) with a `TODO:` comment naming the ceiling and upgrade path (`# TODO: global lock, per-account locks if throughput matters`).

## Output

Code first. When you cut something, say so in at most three short lines: what
was left out, when to add it, and what verified it. Cut nothing → skip the
notes. No essays, no feature tours, no design notes. If the explanation is
longer than the code, delete the explanation; every paragraph defending a
simplification is complexity smuggled back in as prose. Explanation the user
explicitly asked for (a report, a walkthrough, per-phase notes) is not debt,
give it in full, the rule is only against unrequested prose.

Pattern, when there was a cut: `[code] → skipped: [X], add when [Y]. verified: [check].`

## When not to cut

Never simplify away: input validation at trust boundaries, error handling
that prevents data loss, security measures, accessibility basics, tests for
the behavior you changed, anything explicitly requested. User wants the full
version → build it, no re-arguing.

Tests are not scaffolding. A diff that drops the test proving the fix works
isn't smaller, it's unverified, and the next person pays for it.

Never cut understanding. The ladder shortens the solution, never the reading.
Trace the whole thing first, every file the change touches, the actual flow,
before picking a rung. Skipping comprehension to ship a small diff is the
dangerous failure: it dresses up as efficiency and ships a confident wrong
fix. Read fully, then cut.

Code that touches the physical world needs a calibration knob a minimal model
can't infer: real clocks drift, real sensors read off, real actuators run a
few percent fast. Leave the tuning parameter, not just less code.
