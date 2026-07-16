---
name: never-touch-live-billing
description: The billing service is off-limits without Maya's explicit go-ahead, even for safe-looking refactors
metadata:
  type: feedback
---

No changes to `apps/billing/` or anything that imports it without Maya's
explicit go-ahead in the current session — including "safe" refactors,
dependency bumps, and lint fixes. Rule set 2026-06-09 after a type-only
refactor changed a Stripe webhook payload shape and double-charged two
pilot customers (refunded same day).

**Why:** billing bugs cost trust with the exact pilots Driftwatch needs as
references (see [[driftwatch-mvp-state]]), and the webhook path has no
staging equivalent — staging shares prod Supabase (see
[[driftwatch-stack]]).
**How to apply:** if a task requires touching billing, stop, state exactly
which files and why, and wait for an explicit yes before editing.
