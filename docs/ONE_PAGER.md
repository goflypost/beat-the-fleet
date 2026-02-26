# Alternate Corridor Test — Ops One-Pager

**Product area:** Rider Engagement / Growth Ops
**Status:** Concept / UI prototype
**Audience:** Cross-functional review (Eng, Safety, Data, PM)

---

## Objective / Problem

Autonomous ride platforms compute baseline routes from aggregate fleet data, but have no rider-facing feedback loop for alternate corridors. When a rider's actual path outperforms the baseline, the signal is lost — not surfaced to the rider, not closing any engagement loop.

**Result:** missed retention opportunity and incomplete corridor-quality data.

---

## Hypothesis

Surfacing a post-ride scorecard (corridor vs baseline, time delta) and issuing Ride Credit when a corridor meaningfully outperforms baseline — under verified safe-driving conditions — will increase 7-day rider retention and generate labeled corridor-performance data at low marginal cost.

---

## User Journey / Flow

| Step | Event | System action |
|---|---|---|
| 1 | Rider starts trip | Baseline route ETA computed and logged |
| 2 | Rider takes alternate corridor | Route deviation detected, both corridors tracked |
| 3 | Trip ends | Corridor vs baseline comparison computed |
| 4 | Safety gate check | Speed compliance, harsh-event check run |
| 5 | Scorecard delivered | Map overlay + time-split + delta surfaced in-app |
| 6 | Credit issued (if eligible) | Ride Credit applied to account if outperformed threshold |
| 7 | Next session | Credit drives re-engagement |

---

## Operations Plan and Phased Rollout

| Phase | Scope | Duration | Exit Criteria |
|---|---|---|---|
| 0 — Internal pilot | Internal / employee riders | 1 week | No safety flags; UI feedback positive |
| 1 — Geo pilot | Single city, opt-in, 5% treatment cohort | 4 weeks | +3 pp retention vs control; guardrails green |
| 2 — City expand | Full city, 50% rollout | 4 weeks | Retention holds; fraud rate below threshold |
| 3 — Multi-city | Top 3 markets by ride volume | 6 weeks | Consistent signal across geos |
| 4 — Full launch | All active markets | Ongoing | Standard A/B monitoring; quarterly review |

Go/no-go at each phase requires sign-off from Safety, Data, and Ops leads.

---

## Safety and Fraud Controls

**Safety gates (hard stops — credit ineligible if any triggered):**
- Speed compliance: no segments materially above posted limit
- Harsh events: no harsh braking or acceleration events flagged
- Minimum ride duration: ride below threshold duration is excluded

**Fraud controls:**
- Duplicate corridor detection: same origin-destination pair submitted more than N times per day per account flagged for review
- Corridor plausibility check: alternate path must resolve to valid road segments
- Credit velocity cap: maximum credit issuance per account per rolling period

No credit is issued if any safety gate fails. Safety incident rate is a primary guardrail — any degradation halts rollout.

---

## A/B Test Design

| Dimension | Detail |
|---|---|
| Randomization unit | Rider account |
| Treatment | Post-ride scorecard UI + Ride Credit eligibility |
| Control | Standard post-ride confirmation (status quo) |
| Primary metric | 7-day rider retention rate |
| Secondary metrics | Ride frequency, avg inter-session gap, NPS delta |
| Guardrail metrics | Safety incident rate, credit fraud rate, ETA accuracy |
| MDE | +3 percentage points at 80% power, two-sided |
| Sample size (est.) | ~10,000 riders per arm (varies by city) |
| Runtime | 4–6 weeks |
| Stop conditions | Safety guardrail breach OR p(harm) > threshold |

---

## Measurement / Metrics and Instrumentation Events

**Primary KPI:** 7-day retention (rider completes >=1 ride in 7 days post-treatment exposure).

**Instrumentation events to log:**

| Event | Trigger | Key properties |
|---|---|---|
| `corridor_test_start` | Rider deviates from baseline route | `rider_id`, `trip_id`, `baseline_eta`, `corridor_id` |
| `safety_gate_result` | Post-trip safety check completes | `trip_id`, `passed`, `fail_reason` |
| `scorecard_shown` | Scorecard UI displayed | `trip_id`, `time_delta_secs`, `user_won` |
| `scorecard_dismissed` | Rider closes scorecard | `trip_id`, `dwell_ms` |
| `credit_issued` | Ride Credit applied | `rider_id`, `trip_id`, `credit_pct`, `tier` |
| `credit_redeemed` | Credit applied to a subsequent ride | `rider_id`, `original_trip_id` |

---

## Risks and Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Safety gates gamed (slow routes to avoid harsh events) | Low | High | Monitor speed-profile distribution; add minimum avg speed gate |
| Credit fraud (coordinated route manipulation) | Medium | Medium | Velocity cap, plausibility check, manual review queue |
| Rider confusion (complex scorecard) | Medium | Low | Usability test in Phase 0; simplify if needed |
| ETA model degradation (noisy corridor data) | Low | Medium | Separate corridor-test data pipeline; do not feed raw into baseline model without review |
| Disengagement if credit tiers feel unattainable | Medium | Medium | Calibrate tiers against real corridor distribution; iterate post Phase 1 |

---

## Next Steps

1. Align Safety and Data on gate thresholds and guardrail definitions.
2. Instrument event taxonomy in analytics pipeline.
3. Define credit tier values with Finance / Product.
4. Recruit Phase 0 internal pilot participants.
5. Build A/B assignment and scoring service (backend, out of scope for this demo).
6. Launch Phase 1 geo pilot and monitor daily.
