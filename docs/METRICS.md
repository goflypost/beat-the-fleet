# Metrics Reference — Alternate Corridor Test

**Product area:** Rider Engagement / Growth Ops
**Version:** 1.0 (concept / prototype stage)

---

## KPI Definitions

| KPI | Definition | Owner | Target direction |
|---|---|---|---|
| 7-day rider retention | % of treatment riders completing >=1 ride within 7 days of scorecard exposure | Engagement Ops | Up |
| Ride frequency | Rides per rider per week (rolling 4-week window) | Growth Ops | Up |
| Avg inter-session gap | Median hours between consecutive rides per rider | Engagement Ops | Down |
| Rider NPS delta | Treatment NPS minus control NPS (in-app survey, sampled) | Product | Up |
| Corridor data contribution rate | % of trips with a usable alternate path signal (deviation detected + safety gate passed) | Data | Up (toward saturation) |
| Scorecard engagement rate | % of scorecard impressions with dwell time > 10 seconds | Product | Monitor only |

---

## Guardrail Metrics

Guardrail metrics must not degrade. Any significant degradation triggers a hold on rollout progression.

| Guardrail | Definition | Threshold | Action if breached |
|---|---|---|---|
| Safety incident rate | Reported safety incidents per 1,000 rides | Must not increase vs baseline | Immediate hold; Safety review |
| Credit fraud rate | % of credits flagged as duplicate or implausible | < 0.5% of issued credits | Hold credit issuance; ops investigation |
| ETA accuracy | Median absolute deviation of estimated vs actual trip time | Must not degrade vs control | Data review; pause corridor data ingestion |
| Platform availability | Scorecard service uptime | >= 99.5% | Eng escalation |

---

## Event Taxonomy

All events should be logged to the analytics pipeline with a `experiment_variant` property (`treatment` or `control`) and `rider_id` for joining.

### Core instrumentation events

| Event name | Trigger | Required properties | Optional properties |
|---|---|---|---|
| `corridor_test_start` | Rider route deviates from baseline | `rider_id`, `trip_id`, `baseline_eta_secs`, `corridor_id`, `deviation_start_ts` | `city`, `time_of_day_bucket` |
| `safety_gate_result` | Post-trip safety evaluation completes | `trip_id`, `passed` (bool), `fail_reason` (enum or null) | `speed_violation` (bool), `harsh_event_count` |
| `scorecard_shown` | Scorecard UI rendered to rider | `trip_id`, `rider_id`, `time_delta_secs`, `user_won` (bool), `experiment_variant` | `credit_tier` (if eligible) |
| `scorecard_dismissed` | Rider closes scorecard screen | `trip_id`, `dwell_ms` | `action` (e.g. `swipe_down`, `tap_close`) |
| `credit_issued` | Ride Credit applied to rider account | `rider_id`, `trip_id`, `credit_pct`, `tier_label`, `issued_ts` | `corridor_id` |
| `credit_redeemed` | Credit applied to a subsequent booking | `rider_id`, `original_trip_id`, `redemption_trip_id`, `redeemed_ts` | |
| `credit_expired` | Credit expires unused | `rider_id`, `original_trip_id`, `credit_pct`, `issued_ts`, `expired_ts` | |
| `fraud_flag_raised` | Automated fraud check flags a credit | `rider_id`, `trip_id`, `flag_reason` | |

### Engagement / session events (standard, confirm coverage)

| Event name | Trigger |
|---|---|
| `app_session_start` | Rider opens the app |
| `ride_requested` | Rider submits a ride request |
| `ride_completed` | Trip marked complete |
| `nps_survey_submitted` | In-app NPS survey response received |

---

## A/B Test Design Details

### Setup

| Parameter | Value |
|---|---|
| Experiment type | Rider-level randomization (holdout-style) |
| Assignment | Persistent; assigned at first qualifying trip |
| Treatment | Scorecard UI shown + credit eligibility enabled |
| Control | Standard post-ride confirmation screen |
| Traffic split | 50/50 treatment/control within opted-in cohort |
| Geo scope | Single city (Phase 1 pilot) |

### Sample Size Assumptions

| Parameter | Assumption |
|---|---|
| Baseline 7-day retention | 40% (illustrative; replace with actuals) |
| MDE | +3 percentage points (absolute) |
| Statistical power | 80% |
| Significance level | 5% (two-sided) |
| Estimated sample per arm | ~10,000 riders |
| Estimated runtime | 4–6 weeks (depending on daily active rider volume) |

*Sample size estimates should be re-computed with actual baseline retention data before launch.*

### Success Criteria

The experiment is considered successful if **all** of the following hold at the end of the pre-specified runtime:

1. 7-day retention rate in treatment is >= +3 pp vs control with p < 0.05 (two-sided).
2. No guardrail metric shows a statistically significant degradation.
3. Credit fraud rate < 0.5% of issued credits.

### Stop Conditions (early termination)

| Condition | Action |
|---|---|
| Safety incident rate significantly higher in treatment | Immediate halt; Safety review |
| Credit fraud rate > 2% of issued credits | Halt credit issuance; ops investigation |
| Scorecard service error rate > 1% | Eng escalation; possible rollback |
| Statistically significant harm on any guardrail | Hold; cross-functional review before continuing |

### Analysis Plan

- Primary analysis: two-sample proportion test on 7-day retention at end of runtime.
- Secondary analyses: Mann-Whitney U on ride frequency and inter-session gap; NPS delta via survey data join.
- Segmentation cuts (exploratory, not primary): city tier, time-of-day, corridor type.
- No peeking / interim stops except for guardrail monitoring (pre-specified).
- Intent-to-treat analysis; no exclusions post-assignment except fraud-flagged accounts.
