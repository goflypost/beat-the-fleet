# Beat the Fleet 🏁

> *The AI thinks it knows the fastest way. Prove it wrong. Get paid.*

A product concept and interactive mockup for gamifying openpilot — built with the comma four in mind.

---

## The Problem Nobody's Talking About

openpilot knows the statistically optimal route. But the people driving with it every day know things the model doesn't: the side street that clears by 9am, the light that runs long on Tuesdays, the cut-through that shaves four minutes off a commute nobody has ever logged.

That knowledge gets used once, then disappears.

**Beat the Fleet puts it to work.**

---

## How It Works

No setup. No pre-selecting a route. You just drive.

When you deviate from openpilot's suggested path, the system notices. It silently runs both timelines: your actual route and the fleet's recommended route, and compares them at the end of your drive.

Beat the fleet by a minute or more and you earn credit off your comma prime subscription. The bigger the gap, the bigger the cut.

| Beat the fleet by | Reward |
|---|---|
| Under 1 min | No reward — close though |
| 1 – 2 min | **10% off** next prime month |
| 3 – 4 min | **15% off** next prime month |
| 5+ min | **50% off** next prime month |

---

## Why This Is Actually Smart

**For drivers:** you're not just supervising anymore. Every time you take your own turn, there's a scorecard waiting at the end. Local knowledge becomes something worth keeping track of.

**For the model:** every deviation is already being logged. Beat the Fleet just closes the loop by surfacing that comparison back to the driver with a reward attached. Shortcuts that win get absorbed into training data. Routes that lose confirm the AI was right. Either way, comma gets human-verified route data from people who are motivated to try. The cost of a 15% prime credit is nothing compared to what a real local shortcut is worth across the fleet.

**For growth:** drivers will talk about it. They'll try routes they've been curious about. They'll check the leaderboard. That's engagement and retention that compounds.

---

## The Mockup

`beat-the-fleet.html` — open it in any browser, no dependencies, no build step.

Simulates two states:

- **Active drive:** openpilot HUD with a subtle deviation tracking indicator running in the corner
- **Post-ride reveal:** sequential scorecard that shows your route vs the fleet route on a live map, the time split, and the reward unlock if you won

Also includes an all-time local legends leaderboard and a fleet-wide stats ticker.

---

## Status

Concept and working prototype. Not affiliated with comma.ai — just someone who thinks this should exist.

---

*Built by [@goflypost](https://github.com/goflypost) · GPL-3.0*
