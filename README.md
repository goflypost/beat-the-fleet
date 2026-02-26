# Rider Engagement Ops — Alternate Corridor Test

> A Rider Engagement Operations portfolio piece demonstrating a post-ride alternate-corridor feedback loop for an autonomous ride platform.

> **This repo is a UI concept demo** — no real telemetry, routing, or fleet integration.

---

## Problem Statement

Autonomous ride platforms accumulate aggregate routing data but lack a rider-in-the-loop signal for evaluating alternate corridors at a hyperlocal, time-of-day level. When a rider's actual path outperforms the baseline ETA, that information is either lost or absorbed into model retraining cycles without surfacing any value back to the rider.

**Gap:** no closed feedback loop between rider corridor outcomes and rider engagement.

---

## Hypothesis

If riders receive a post-ride scorecard comparing their corridor to the platform baseline, and earn time-on-platform credit when their corridor meaningfully outperforms (within safe-driving thresholds), then:

- Riders will retain higher session frequency (+engagement).
- The platform accrues labeled corridor-performance data at low marginal cost.
- Riders develop a sense of agency and local expertise, improving satisfaction (NPS).

---

## Rider Engagement Loop

```
Ride starts -> baseline route computed
     |
Rider takes alternate corridor (organic or suggested)
     |
Post-ride: corridor vs baseline outcome compared
     |
Scorecard surfaced in-app (time delta, map overlay)
     |
If corridor outperforms AND safety thresholds met -> Ride Credit issued
     |
Credit drives next session -> loop closes
```

---

## Safety Gating and Fraud Controls

Any production implementation must enforce hard safety gates **before** credit is computed:

| Check | Condition | Action if failed |
|---|---|---|
| Speed compliance | No segments over posted limit | Ineligible for credit |
| Harsh events | No harsh brake/acceleration events | Ineligible for credit |
| Minimum ride duration | Ride length >= threshold to prevent gaming | Discard |
| Corridor plausibility | Alternate path is a real road segment | Discard |
| Duplicate detection | Same corridor submitted >X times/day | Flag for review |

This demo does not implement any of the above — it is a UI concept only.

---

## Experiment Design (A/B Framing)

| Dimension | Detail |
|---|---|
| Unit of randomization | Rider account (not trip) |
| Treatment | Post-ride scorecard + credit eligibility |
| Control | Post-ride confirmation screen only (current state) |
| Primary metric | 7-day rider retention rate |
| Secondary metrics | Ride frequency, avg session gap, NPS delta |
| Guardrail metrics | Safety incident rate, fraud/duplicate credit rate |
| Minimum detectable effect | +3 pp retention at 80% power |
| Estimated runtime | 4-6 weeks (geo pilot, single city) |

---

## Metrics and Measurement

**Primary KPI:** 7-day rider retention (% of riders completing >=1 ride in the following 7 days).

**Secondary KPIs:**
- Ride frequency (rides/rider/week)
- Average inter-session gap (hours)
- Rider NPS delta (treatment vs control)
- Corridor data contribution rate (% rides with usable alternate path signal)

**Guardrails (must not degrade):**
- Safety incident rate per 1,000 rides
- Credit fraud rate (duplicate/suspicious redemptions)
- Platform ETA accuracy (model must not degrade from corridor noise)

---

## Rollout Plan

| Phase | Scope | Duration | Exit Criteria |
|---|---|---|---|
| 0 — Internal pilot | Employees / internal riders | 1 week | No safety flags, UI feedback collected |
| 1 — Geo pilot | Single city, opt-in riders, 5% of cohort | 4 weeks | Retention uplift >= MDE, guardrails green |
| 2 — City expand | Full city, 50% rollout | 4 weeks | Retention holds, fraud rate < threshold |
| 3 — Multi-city | Top 3 markets | 6 weeks | Consistent signal across geos |
| 4 — Full launch | All markets | Ongoing | Standard monitoring |

---

## What the Demo Is and Is Not

**Is:** A static HTML/CSS/JS UI mockup demonstrating the post-ride scorecard interaction pattern — map overlay, time-split comparison, credit reveal, and leaderboard.

**Is not:** Connected to any real fleet, telemetry system, routing engine, or payment/credit system. All data is simulated. No brand integration.

---

## Live Demo (GitHub Pages)

https://goflypost.github.io/beat-the-fleet/

---

## Files

| File | Purpose |
|---|---|
| `index.html` | Interactive UI demo (no dependencies, no build step) |
| `docs/ONE_PAGER.md` | Ops one-pager for cross-functional review |
| `docs/METRICS.md` | KPI definitions, event taxonomy, A/B test design |
| `.github/copilot-instructions.md` | Repo-level Copilot guidance |
| `.github/workflows/pages.yml` | Static GitHub Pages deployment |

---

*Built by [@goflypost](https://github.com/goflypost) · GPL-3.0*
