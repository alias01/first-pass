# CODE_REVIEW_STANDARDS.md — structure to generate in Phase 1

Fill this in per-project and write it as the actual file. Keep entries terse — this is a reference the reviewer reads every time, not documentation for humans.

```markdown
# Code Review Standards — <project name>

Established: <date>. Update via the ai-pr-review skill, Phase 1, when conventions change.

## Stack
<languages, frameworks, key libraries detected>

## Adopted rules (enforced in review)
| Rule | Rationale | Source |
|---|---|---|
| e.g. "New error handling uses Result<T,E>, not throw" | Matches existing 80% of codebase | Existing convention |
| e.g. "No `any` types in new TS code" | Low-effort, high-value; doesn't touch existing code | Google TS style guide |

## Deferred / worth discussing later (not enforced — do not flag in review)
| Rule | Why it's deferred |
|---|---|
| e.g. "Standardize all API handlers to the newer middleware pattern" | Would require rewriting ~40 existing handlers; only worth it as a dedicated project |

## Explicitly rejected
| Rule | Why we're not adopting it |
|---|---|
| e.g. "Airbnb's default export ban" | Codebase already uses default exports consistently; not worth churn |
```

Notes for whoever (re)generates this file:
- Every "Adopted" rule must be something the reviewer can actually check mechanically or by pattern-matching against real code — vague rules ("write clean code") aren't enforceable and shouldn't go in.
- Keep "Deferred" and "Rejected" sections populated, not deleted — they prevent the same debate from resurfacing every time a big-company guide is checked again later.