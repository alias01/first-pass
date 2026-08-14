---
name: first-pass
description: Do a first-pass code review on a diff, PR, or file(s) before a human looks at it — catching correctness issues, deviations from the codebase's own conventions, and AI-generated-code smells (overengineering, inconsistency with existing patterns, defensive overkill, hallucinated APIs). Use this whenever the user asks to review code, review a PR, review a diff, "check this before I merge," or wants a pre-review pass on AI-written code. Also use it when the user wants to establish, update, or discuss a project's code review standards / rule set. Works with any language or stack — detects it from the code and repo.
---

# AI PR Review

A first-pass reviewer meant to catch 80% of what a human reviewer would flag, so the human's final pass is a glance, not a read-through. It runs in two phases: **Phase 1** happens once per project and produces a durable standards file; **Phase 2** is the actual review, run every time, and reads that file instead of re-deciding conventions each time.

## Phase 1 — Establish codebase standards (once per project, or on request)

**When to run this phase:**
- A review is requested but no standards file exists yet for this project (see below) — this phase is **mandatory** in that case, not optional. Phase 2 must never review against silently-assumed conventions; skipping straight to a review means every "standards" finding is a guess.
- The user explicitly asks to set up, review, or update the project's standards

**Where the standards file lives:** look for, in order: `.claude/CODE_REVIEW_STANDARDS.md`, `CODE_REVIEW_STANDARDS.md` at repo root, or ask the user if they already have a rule set for AI review elsewhere (a wiki page, a `CONTRIBUTING.md`, a linter config they consider authoritative, etc.) and offer to work from that instead of building a new one.

**Steps:**

1. **Ask first.** Before inferring anything, ask the user directly: "Do you already have a rule set or style guide you want AI reviews to follow?" If yes, use it as-is (skip to step 5) unless they want it revisited.

2. **If no rule set exists, infer current conventions from the whole repo, not a sample of convenience.** Walk the actual tree — every top-level module/package/layer, not just the files that happen to be open or recently changed. For each layer, read config files (linter/formatter configs, `package.json`/`pyproject.toml`/etc.), a representative file or two, and tests if present. Note: naming conventions, file/folder structure, error-handling pattern, how state/config is passed around, testing patterns, formatting, which libraries are actually used vs. avoided, and any repeated idioms. Where a convention is inconsistent across the repo (e.g. two error-handling styles coexist), note that explicitly rather than picking whichever file you saw first — this becomes "current practice."

3. **Identify the stack and look up the relevant big-company standard.** Detect language(s)/framework(s) in use, then web-search the standard style guide(s) that apply (e.g. Google's JS/TS or Python style guide, Airbnb JS, PEP 8, Go's Effective Go, etc. — whichever fits the detected stack). Pull the parts that are actually relevant to what this codebase does; don't dump the whole guide.

4. **Compare current practice against the big-company standard and classify every divergence by effort AND impact:**
   - **Adopt now** — low effort *and* low-to-medium impact: forward-only changes (lint rule additions, naming conventions applied to new code, patterns enforced going forward) that don't require touching existing code. Propose these as pre-selected/default-yes — the user can still veto, but they shouldn't have to argue for the obvious ones.
   - **Consider later** — high effort and/or high impact: anything that would mean rewriting a meaningful slice of existing code, or that changes a pattern pervasive enough to affect how the whole codebase reads (restructuring error handling, renaming a pervasive pattern, swapping a core library). These are exactly the divergences from big-company practice that are easy to miss and expensive to reverse later — don't silently adopt or silently reject them. Surface each one as its own explicit decision: what the codebase does today, what the standard says, and why it matters, then let the user decide.

   Never decide either bucket silently, but weight the discussion toward the second: present a table — **Rule | Current practice | Big-company recommendation | Effort | Impact | Suggested verdict** — and for every "Consider later" row, wait for an explicit per-row decision (adopt / defer / reject) rather than accepting a blanket "looks good" as covering it. Blanket agreement is fine for the "Adopt now" rows only.

5. **Write the finalized rules** to `CODE_REVIEW_STANDARDS.md` (create `.claude/` first if that's the convention in this environment, otherwise repo root — ask if unclear). Use `references/standards-template.md` as the structure. This file is the source of truth Phase 2 reads every time — don't ask the user to re-explain conventions on future reviews.

## Phase 2 — Review a diff or file(s)

**Steps:**

1. **Load standards.** Read `CODE_REVIEW_STANDARDS.md` if it exists. If it doesn't exist, stop and run Phase 1 first — do not fall back to a standards-less review by default. The only exception: the user explicitly says they want a one-off, standards-blind pass (e.g. "skip the standards stuff, just review this diff") — confirm that's what they mean, since it means the "codebase standards" lens in step 3 below is skipped entirely for this run.

2. **Get the code.** Auto-detect based on what's available:
   - Shell + repo access → run `git diff` against the appropriate base (ask which branch/range if not obvious, e.g. `main...HEAD` vs. staged changes).
   - No shell/repo access → use whatever diff, pasted code, or uploaded file(s) are in the conversation.
   - If neither is available, ask the user how they'd like to provide the code — don't guess.

3. **Review against three lenses**, in priority order (full detail in `references/review-checklist.md` and `references/ai-code-smells.md`):
   - **Correctness & safety** — logic errors, edge cases, error handling, security/data-handling issues. Always the highest priority regardless of stack.
   - **Codebase standards** — check against `CODE_REVIEW_STANDARDS.md`. Don't re-relitigate things already marked "consider later" there — those are known and deliberately deferred.
   - **AI-generated-code smells** — overengineering, inconsistency with existing patterns, defensive overkill, hallucinated/unverified APIs, dead code, comment noise. See `references/ai-code-smells.md` for the full list.

4. **Output as a single scannable table**, most severe first:

   | File | Line | Severity | Issue | Suggested fix |
   |---|---|---|---|---|

   Severity is one of: **Blocking** (bug, security, breaks standard hard rule), **Should-fix** (real but not merge-blocking), **Nit** (optional, style-only — prefix these clearly so they can be skimmed past). Close with a one-line overall verdict ("Solid, just the null-check on line 40 before merge" / "Needs another pass — three blocking issues").

   Don't comment on things already clean. Silence on a file/section is itself signal that it's fine — the goal is a glance-able report, not exhaustive commentary.

5. If something is ambiguous (e.g. a pattern that looks wrong but might be intentional), ask rather than flag it as broken — false positives erode trust in the tool fast, which defeats the whole point.