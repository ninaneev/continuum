---
name: driftwatch-mvp-state
description: Alerts pipeline live for 3 pilots since 2026-06-30; Slack digest in progress; Northbeam demo 2026-07-24
metadata:
  type: project
---

Driftwatch MVP status as of 2026-07-14: the core alerts pipeline (usage
ingestion → risk score → email alert) is live for 3 pilot customers since
2026-06-30. Current build: the Slack daily digest — worker job exists, the
message formatting and per-workspace settings do not. Rejected 2026-06-12:
real-time alerts (pilots said daily is right; hourly felt like noise).
Hard date: product demo to Northbeam (largest prospect) on 2026-07-24.
Stack details in [[driftwatch-stack]]; billing constraint in
[[never-touch-live-billing]].

**Why:** the Slack digest is the last feature Northbeam asked for by name;
the 2026-07-24 demo decides whether they convert from prospect to pilot #4.
**How to apply:** prioritize digest work over refactors until 2026-07-24,
and keep the daily (not real-time) cadence unless Maya says otherwise.
