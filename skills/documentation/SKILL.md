---
name: documentation
description: >
  Documents code the way the language's own toolchain expects: the native doc
  format per language, contract-first doc comments on every public symbol,
  module-level overviews, examples the test suite runs so they cannot drift,
  and inline comments that explain why rather than what. Use whenever code is
  written, added, refactored, or reviewed, whenever a public API changes, and
  whenever asked to write, fix, or review comments, docstrings, doc comments,
  or API reference documentation.
---

# Documentation

Code states what it does. Documentation states what it is for, when to reach
for it, and how it fails. Write it in the same diff as the code, in the format
the language's doc tool reads, or the next reader never finds it.

## What gets documented

- Every public symbol: function, method, type, constant, module. No exceptions.
- Every non-obvious internal function: branching logic, a subtle invariant, or a name that does not fully give it away.
- Every module or package, as a whole.
- Skip: trivial accessors, generated code.

Match the depth of the codebase you are in. Consistency beats one exhaustive
comment among fifty bare ones.

## Use the native format

Write in the format the language's doc toolchain reads, so the comment renders
on the doc site and appears on editor hover.

Never invent a house comment style when the language already has one. Never
put API reference only in a README; the reader is in an editor, not the repo
root.

## What a doc comment says

Open with the symbol name and a verb, as one complete sentence. Use "reports
whether" for booleans.

Then, when applicable and consistently across the codebase:

- what it does and why it exists
- when to use it, and when not to
- parameters: meaning, units, valid ranges, nullability, ownership
- return values and what they mean
- each failure case and the condition that triggers it, naming the error or exception type
- whether it can panic, throw, or abort, and when
- whether it is safe for concurrent use
- side effects: I/O, mutation of arguments, global state

Cover every item that applies and no item that does not. This is a checklist,
not a template: an entry that does not apply is omitted, never written out as
empty or "none".

Describe the contract, not the implementation. The body changes; the contract
is the promise.

## Style

Be as short as the contract allows and no shorter. Concision cuts words, never
facts: if a caller needs to know it, it stays, however long that makes the
comment. Three dense lines naming every failure case beat one vague line, and
beat a paragraph that restates the signature in prose.

- ASD-STE100 simplified technical English
- Cut padding, not content. No "This function ...", no "Simply", no narrating the parameter list back, no repeating what the types already say.
- NEVER use an em dash. Use a period, a comma, or parentheses.
- State facts, do not hedge. "Returns nil when the key is absent", not "may possibly return nil".
- Name symbols exactly as the code names them, case for case.

## Inline comments

Inline comments explain *why*, never *what*. A line that needs a comment to say
what it does needs a better name or a smaller function instead.

Worth a comment: a non-obvious constraint, a workaround with a link to the bug,
a deliberate performance tradeoff, a spec or RFC reference, a unit that is not
in the name.

## Never write

- Comments restating the code (`i++ // increment i`).
- Commented-out code. Delete it, version control remembers.
- Changelog or attribution comments (`// modified by ... on ...`). Version control remembers.
- A `TODO:` with no condition. Name what unblocks it.
- Documentation describing behavior the code no longer has. A wrong doc is worse than no doc.

## Keep it true

- A behavior change and its doc change go in the same diff. A doc updated later is a doc never updated.
- Deprecate in the format's own convention, naming the replacement and the removal version
- Renaming a symbol means re-reading every doc that names it.

## Documentation is not the thing to cut

Minimal code and documented code are not in tension. The smallest change that
fully solves the problem includes the doc comment on what it added, because an
undocumented public symbol is unfinished, not lean. Doc comments are part of the
deliverable, not unrequested prose.
