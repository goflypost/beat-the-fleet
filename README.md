# Beat the Fleet 🏁

> *The model thinks it knows the best way. Prove there’s a better one. Earn credit.*

A product concept + interactive UI mock for a “fleet baseline vs your actual drive” post-ride reveal.

> This repo is **a UI concept demo** (no real telemetry, routing, or openpilot integration).

---

## What It Is

Drivers build local knowledge: which turn is faster **at this time of day**, which light is brutal on Tuesdays, which connector street is a cheat code.

That knowledge gets used once… then disappears.

**Beat the Fleet is a loop-closer UI:**
- detect that the driver took a different path than a baseline route
- compare outcomes post-ride
- surface a scorecard (and optionally a reward)

---

## Important Notes (Reality + Safety)

- **openpilot does not choose navigation routes** in its typical configuration. This concept assumes a “baseline route” provided by a navigation source (phone nav / maps / fleet aggregate), not by openpilot itself.
- Any real implementation must be **safety-gated**:
  - no rewards for speeding or unsafe maneuvers
  - reward eligibility only when driving behavior stays within safe thresholds (speed compliance, no harsh braking, etc.)

This repo does not implement any of that — it’s just the UI mock.

---

## Reward Tiers (demo logic)

| Beat baseline by | Reward |
|---|---|
| Under 1 min | No reward — close though |
| 1 – 2 min | **10% off** next month |
| 3 – 4 min | **15% off** next month |
| 5+ min | **50% off** next month |

---

## The Mock

`index.html` — open it in any browser (no dependencies, no build step).

Simulates two states:
- **Active drive:** HUD mock with a subtle “tracking deviation” pill
- **Post-ride reveal:** map overlay → time split → reward card → leaderboard

---

## Live Demo (GitHub Pages)

Once GitHub Pages is enabled, your demo will be available at:

- https://goflypost.github.io/beat-the-fleet/

(Use the included workflow under `.github/workflows/pages.yml`.)

---

## Status

Concept + working UI prototype. Not affiliated with comma.ai.

---

*Built by [@goflypost](https://github.com/goflypost) · GPL-3.0*
