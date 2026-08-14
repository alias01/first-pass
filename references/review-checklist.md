# General review checklist

Applied on top of (not instead of) `CODE_REVIEW_STANDARDS.md` and `ai-code-smells.md`. These categories are the language-agnostic baseline — they apply to every codebase regardless of stack, unlike the language/framework-specific rules Phase 1 pulls from big-company style guides. Priority order:

## 1. Correctness
- Does the code do what the diff/PR description claims?
- Edge cases: empty input, null/undefined, boundary values, off-by-ones
- Error paths, not just the happy path
- Race conditions / async ordering issues if concurrency is involved

## 2. Security & data safety
- Input validation on anything crossing a trust boundary (API input, user input, file uploads)
- Auth/permission checks present where expected
- No secrets, keys, or credentials committed
- SQL/command/template injection surfaces
- If the codebase handles sensitive data (PII, health data, payment data, etc.) — extra scrutiny on logging, storage, and transmission of that data specifically

## 3. Design fit
- Does this belong where it's placed, structurally?
- Is it consistent with how the rest of the codebase solves similar problems (cross-reference with `CODE_REVIEW_STANDARDS.md`)?
- Right-sized: not under-engineered (missing needed error handling) and not over-engineered (see `ai-code-smells.md`)
- Could this be simpler? Would another dev easily understand and reuse it later?

## 4. Error handling
- Does this follow the codebase's established error-handling pattern (exceptions vs. result types vs. error codes — check `CODE_REVIEW_STANDARDS.md`), not a one-off style?
- Errors are surfaced/logged/propagated appropriately, not silently swallowed
- Failure modes for external calls (network, disk, third-party APIs) are handled, not assumed to always succeed

## 5. Tests
- Are the meaningful paths covered, not just the happy path?
- Missing tests on risky/critical logic is a legitimate Blocking or Should-fix flag, not a nit
- Tests are well-designed: they'd actually fail if the logic broke, not just exercising the code for coverage's sake

## 6. Naming & readability
- Clear, consistent names for variables, functions, classes, files — matching the codebase's naming convention, not just "clear in isolation"
- Function length/complexity; would someone unfamiliar with this change be able to follow it in six months?

## 7. Documentation & comments
- Comments explain *why*, not *what* — restating the code in prose is noise (see `ai-code-smells.md`)
- Public APIs/exported functions documented where the codebase's convention expects it (not invented where it doesn't)

## Severity guide
- **Blocking**: bug, security issue, breaks a hard rule in `CODE_REVIEW_STANDARDS.md`, missing tests on critical logic
- **Should-fix**: real issue, not merge-blocking — readability, minor design mismatch, missing edge-case handling on low-risk paths, inconsistent error handling
- **Nit**: optional, style-only, or a matter of taste not covered by the standards file