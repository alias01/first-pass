# Language-specific standards — what to look for

Used in Phase 1, step 3 (`Skill.md`) after the stack is detected. This is the checklist of *categories* to pull from the big-company style guide for that language — not the guide itself. The point is to stop "pull the parts that are relevant" from being vague: check each category below against the detected language/framework, and only carry forward ones that actually diverge from current practice or are unaddressed by it.

## Naming conventions
- Case conventions per identifier kind (variables, functions, classes, constants, files) — e.g. `snake_case` vs `camelCase` vs `PascalCase`, and whether the language community treats this as a hard rule (Python/PEP 8) or a looser preference
- Whether the codebase's actual naming matches the language's idiomatic convention, not just internal self-consistency

## Formatting
- Indentation, line length, bracket/brace placement — usually already enforced by a formatter (Prettier, Black, gofmt, rustfmt); if a formatter config exists, defer to it entirely rather than re-litigating by hand
- Whether a formatter is configured at all — its absence is itself a finding worth surfacing (low effort, low impact, easy adopt-now)

## Type system usage
- For typed/optionally-typed languages: strictness settings (e.g. TS `strict`, mypy strictness), use of escape hatches (`any`, `# type: ignore`) and whether the codebase's usage is idiomatic or overused
- Whether type annotations are used where the language convention expects them (public function signatures, exported APIs)

## Imports & module organization
- Import ordering/grouping conventions (stdlib vs. third-party vs. local)
- Circular dependency avoidance, module boundary conventions
- Absolute vs. relative import preference for the language/framework

## Idioms & language features
- Use of modern language features vs. legacy patterns (e.g. `async/await` vs. callbacks/promises in JS, list comprehensions vs. manual loops in Python, composables vs. mixins in Vue 3)
- Immutability conventions where the language supports them (const-by-default, frozen dataclasses, readonly types)
- Concurrency/async patterns idiomatic to the language (goroutines+channels in Go, async/await in JS/Python, not homegrown thread management)

## Comments & documentation format
- Doc-comment format the language/tooling expects (JSDoc, docstrings, godoc) if the codebase publishes or generates docs from comments
- Whether the codebase already uses one consistently — inconsistent formats across files is itself a finding

## Dependency/package management
- Idiomatic package manager and lockfile conventions for the ecosystem (npm/pnpm/yarn, pip/poetry/uv, cargo, go modules)
- Version pinning conventions already in use

## How to use this in Phase 1
For each category above: note current practice, note what the big-company guide for the detected language says, and only add a row to the Phase 1 divergence table (`Skill.md` step 4) if there's an actual gap. A category where current practice already matches the standard needs no entry — this file is a checklist to make sure nothing is missed, not a mandate to produce a rule for every row.
