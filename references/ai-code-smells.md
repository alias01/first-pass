# AI-generated-code smells

Patterns that show up disproportionately often in AI-written code, distinct from ordinary bugs or style issues. Flag these under their own lens in the review — they're about fit and restraint, not correctness.

## Overengineering
- Abstraction layers (interfaces, factories, strategy patterns) built for a single current use case with no second implementation in sight
- Config options / feature flags for things that will never actually vary
- Generic, parameterized solutions where the codebase's existing pattern is a direct, specific one
- New utility functions that duplicate something already in the codebase, just because the AI didn't know it existed — check for this explicitly by searching for similar existing helpers before flagging "missing utility"

## Inconsistency with the existing codebase
- A different error-handling style than the surrounding code (e.g. throwing where the file otherwise returns Result/Either types, or vice versa)
- Different naming convention than the file/module it's dropped into
- Introducing a new library/pattern for something the codebase already has an established way of doing
- Formatting or structure that doesn't match the immediate surrounding code, even if it's "more correct" in isolation

## Defensive overkill
- try/catch or null checks around operations that structurally cannot fail or return null in this context
- Validating the same input at multiple layers redundantly
- Overly broad catch blocks that swallow errors instead of handling the specific failure mode

## Unverified or hallucinated APIs
- Calls to library methods, config options, or language features that should be double-checked against actual docs/types rather than assumed correct — flag anything that looks plausible but unfamiliar for verification rather than asserting it's wrong
- Import statements for packages not in the project's dependency file

## Dead weight
- Unused imports, variables, or now-unreachable branches left over from iteration
- Leftover scaffolding/comments like "// TODO: implement" on code that's actually already implemented, or stale comments describing a previous version of the logic

## Comment noise
- Comments that restate what the code obviously does line-by-line
- Comments narrating the diff ("// added this check", "// new function") instead of explaining why the code exists
- Missing comments where they'd actually help — a genuinely non-obvious business rule or workaround with no explanation

## How to report these
Same severity scale as everything else. Most smells here are **Nit** or **Should-fix**, not **Blocking** — they affect maintainability, not correctness. Don't inflate their severity just because they're common in AI output; judge each on its actual impact.