# CODE_REVIEW_STANDARDS.md — structure to generate in Phase 1

Fill this in per-project and write it as the actual file. Keep entries terse — this is a reference the reviewer reads every time, not documentation for humans.

```markdown
# Code Review Standards — <project name>

Established: <date>. Update via the first-pass skill, Phase 1, when conventions change.

## Stack
<languages, frameworks, key libraries detected>

## Adopted rules (enforced in review)
| Rule | Effort | Impact | Rationale | Source |
|---|---|---|---|---|
| e.g. "New error handling uses Result<T,E>, not throw" | Low | Low | Matches existing 80% of codebase | Existing convention |
| e.g. "No `any` types in new TS code" | Low | Low | Forward-only; doesn't touch existing code | Google TS style guide |

## Deferred / worth discussing later (not enforced — do not flag in review)
| Rule | Effort | Impact | Why it's deferred |
|---|---|---|---|
| e.g. "Standardize all API handlers to the newer middleware pattern" | High | High | Would require rewriting ~40 existing handlers; user chose to defer rather than adopt or reject outright |

## Explicitly rejected
| Rule | Why we're not adopting it |
|---|---|
| e.g. "Airbnb's default export ban" | Codebase already uses default exports consistently; not worth churn |

## Resolved inconsistencies (internal — codebase disagreed with itself)
| Area | Variants found | Chosen standard | Rationale |
|---|---|---|---|
| e.g. "Error handling in API layer" | `throw` in `/api/legacy/*`, `Result<T,E>` in `/api/v2/*` | `Result<T,E>` | Newer code (per git history) and matches adopted big-company recommendation above |
```

Notes for whoever (re)generates this file:
- Every "Adopted" rule must be something the reviewer can actually check mechanically or by pattern-matching against real code — vague rules ("write clean code") aren't enforceable and shouldn't go in.
- Keep "Deferred" and "Rejected" sections populated, not deleted — they prevent the same debate from resurfacing every time a big-company guide is checked again later.
- "Resolved inconsistencies" exists because the codebase itself may have never picked a single pattern — flag these during Phase 1 exploration even if no external big-company guide addresses the specific case; internal consistency matters independent of matching an outside standard.