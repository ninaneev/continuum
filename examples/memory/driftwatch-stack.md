---
name: driftwatch-stack
description: Next.js 15 + Supabase Postgres + Fly.io worker; pnpm only; staging shares the prod Supabase project
metadata:
  type: reference
---

Driftwatch runs as a monorepo: Next.js 15 app (`apps/web`), a Node worker
for ingestion and scoring (`apps/worker`) deployed on Fly.io, and Supabase
Postgres for storage and auth. Package manager is pnpm — npm and yarn
lockfiles must never appear in the repo. Non-obvious trap: staging and
production share the same Supabase project, separated only by schema
(`staging.*` vs `public.*`), so "it's just staging" is never a safe
assumption for destructive SQL. Billing (`apps/billing/`) is a separate
Stripe-backed service — see [[never-touch-live-billing]] before going near
it.
