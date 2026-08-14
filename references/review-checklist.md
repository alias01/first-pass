# General review checklist

Applied on top of (not instead of) `CODE_REVIEW_STANDARDS.md` and `ai-code-smells.md`. Priority order:

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

## 4. Tests
- Are the meaningful paths covered, not just the happy path?
- Missing tests on risky/critical logic is a legitimate Blocking or Should-fix flag, not a nit

## 5. Readability & maintainability
- Naming clarity, function length/complexity
- Would someone unfamiliar with this change be able to follow it in six months?

## Severity guide
- **Blocking**: bug, security issue, breaks a hard rule in `CODE_REVIEW_STANDARDS.md`, missing tests on critical logic
- **Should-fix**: real issue, not merge-blocking — readability, minor design mismatch, missing edge-case handling on low-risk paths
- **Nit**: optional, style-only, or a matter of taste not covered by the standards file