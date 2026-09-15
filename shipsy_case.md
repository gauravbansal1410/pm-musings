# The hub is eating the day

*Shipsy Senior PM — Case study*

## Assumptions

- Rider pay is a mix of a fixed guarantee (minimum pay) + per-parcel earnings + incentive slabs.
- Parcels arrive at the hub overnight and are sorted there before riders check in.
- Riders return to the hub at the end of the day to settle cash from cash-on-delivery orders and hand back that day's undelivered and return parcels, so nothing carries into the next morning.

---

## Q1 — Framing, metric, guardrails

This case bundles two separate problems under one read — that riders are slow and undisciplined, and a leaderboard is expected to fix both. I'd want to test that hypothesis before building anything.

### Problem 1: 2 hours 40 minutes before the first delivery attempt

Implies riders are slow and a leaderboard would somehow make them faster. Before accepting that, I'd check:

- What does the current allocation system use to assign routes — proximity, historical speed, tenure, or random?
- Is hub dwell time uniform across the 40 hubs, or concentrated in a handful?

**Primary metrics**
- **Hub dwell time** — check-in to hub-exit event timestamp.
- **Prediction error** — gap between the system's predicted first-attempt time and the actual, once hub exit is known.

**Guardrails:** hub handover accuracy (parcel count discrepancies at pickup); delivery accuracy.

**Monitor:** line-haul truck on-time arrival rate, to check whether the constraint is even inside the hub's control.

### Problem 2: new riders complete 40% fewer deliveries than tenured riders, and 90-day churn is high

Implies newer riders are inefficient and less motivated, and a leaderboard would fix both. Before accepting that, I'd check:

- What's the current new-vs-tenured rider mix, and is it shifting as churn rises?
- Referral-driven signup share of new joinees? Voluntary vs. platform-initiated churn split?
- Is there any bias in the allocation system for newbie riders?

**Primary metrics**
- **90-day churn rate** — % of a hired cohort no longer active at day 90, matured cohorts only.
- **Ramp speed** — median weeks for a rider to reach the tenured P10 (bottom-decile) productivity floor, computed only among riders who didn't churn. A fast ramp among a handful of survivors is not a good outcome — these two numbers are read together, never separately.

**Guardrails:** delivery accuracy of the new-rider cohort; safety incidents of the new-rider cohort.

**Monitor:** W1 / W4 / W8 churn rate, to see whether losses cluster early or build gradually toward day 90; P10/P50/P75/P90 income levels of new riders per week.

---

## Q2 — Breaking down the 2 hours 40 minutes

![Three-lane process map showing tracked steps and open questions from truck arrival through first delivery attempt](assets/q2_diagram.png)

The clock the head of ops sees has two points: 8:00am check-in and 10:40am first attempt. Everything between is currently one unmeasured block. I suspect the real constraint is sorting and truck arrival timing — it's the largest single block, and it sits upstream, impacting all riders equally. That's one reason I'd expect a leaderboard to help here less than it might for the new-rider gap in Problem 2 — more on that in Q5.

**Open questions I'd need answered before trusting any of this:**

1. How are parcels sorted and arranged for handover — by size, by first-stop order, or unsorted? If riders manually reorganize a bag before leaving, that time sits inside the handover block, not the ride block, and it changes where we'd intervene.
2. Is there proof of handover confirming a rider's parcel count matches what was assigned? Do they photograph every parcel they carry?
3. How far is the average first stop from each hub?
4. Is arrival at the first stop geofenced, or self-reported? If self-reported, our first-attempt timestamp is unreliable at the source.
5. Have customers been notified of an expected delivery window in advance, and can an unresponsive customer delay a rider's attempt?
6. Do riders see a predicted or expected delivery time, and is there a penalty for missing it? If yes, that's a second live incentive against slowness, on top of per-parcel pay — which cuts directly against the leaderboard proposal.
7. How often do riders deviate from the assigned route or make unplanned stops, and do we track that at all?
8. Does Shipsy operate its own fleet primarily, or rely on third-party riders, across the 8 cities — and if third-party, does service quality vary by pin code or provider?

**What nobody is measuring today:** hub-level timestamps for truck arrival, sort completion, handover scan, and exit scan, across all 40 hubs — paired with the operational questions above, since the timestamps alone tell us a block is long, not why.

---

## Q3 — The 40% gap, explanations that aren't about effort

**1. New riders don't yet know how the job actually works.**

- Don't know the hub process yet — where to collect parcels, what to do at each step — so they spend more time than they need to.
- Run into situations they haven't seen before — a locked gate, a wrong address, a refused delivery — and have to stop and call support to figure out what to do, several times a day, where a tenured rider already knows.
- Don't know the local area — no shortcuts, no sense of which lanes are faster.
- Language gaps — with the app, with customers, even during training — that slow down every interaction a little.
- Extra careful with parcels and returns because the habit isn't built yet, which is slower but not lower effort.
- Slower at cash handling for cash-on-delivery orders until they've done it enough times.
- Basic friction with their phone or device — battery, app quirks — that a tenured rider has already learned to work around.

*How to tell this apart from low effort:* compare how long the same task takes for a new rider versus a tenured rider, same hub, similar route, similar delivery type. If new riders are specifically slow at a handful of known sticking points, that's unfamiliarity, not laziness. A short conversation with a handful of new riders would likely confirm most of this within a week.

**2. New riders may be getting harder routes, not just working them less well.**

If route assignment leans on tenure or past speed, new riders could get longer or less efficient areas, and a lower delivery count would fairly reflect a harder assignment, not weaker effort.

*How to tell this apart from low effort:* compare new and tenured riders only when they're on matched routes — same stop count, similar distance, similar area. If the gap shrinks once the route is held constant, that's the route, not the rider.

**3. Trust issue about potential income** — new riders have no credible way to know what's actually achievable, only what the recruiting agency told them.

A tenured rider's real earnings, on a real day, on a real route, would tell a new rider whether ₹1000/day is a realistic target or a recruiting pitch. Today they have no way to check that. If a new rider doesn't fully trust the number they were promised, they have a reasonable reason to hold back rather than push toward a target they're not sure is real.

*How to tell this apart from low effort:* ask a sample of new riders what income they expected, and whether they believe the promised income is actually achievable. If a meaningful share doubt it, that's a trust and credibility problem, not a laziness problem — and it would persist even for a rider who's already fast and capable.

*Caveat on the comparison itself, not a fourth explanation:* the tenured group being compared against has already lost its slowest riders over time, since the slow ones tend to leave first — survivorship bias. That alone can make the 40% look larger than the real gap.

---

## Q4 — What to ship, and what not to

**Problem 1:** hub-level event tracking — truck arrival, sort completion, handover scan, exit scan — live across all 40 hubs within two weeks. This is a logging exercise against data the routing system likely already generates, not new build.

**Problem 2 — fixes mapped directly to the explanations in Q3:**

- **For unfamiliarity (explanation 1):** a lightweight onboarding pilot in 3–4 hubs — a buddy-rider system for the first week, plus an in-app FAQ for the situations new riders currently call support about. Cheap, fast, and targets exactly what we think is costing time.
- **For route difficulty (explanation 2):** route-difficulty tagging (stop count, distance, parcel density) on every route, so any comparison between new and tenured riders — in the onboarding pilot, in the leaderboard test below, in ongoing reporting — is done on matched routes, not raw totals. This is infrastructure, not a rider-facing feature, but nothing else in Problem 2 is trustworthy without it.
- **For income credibility (explanation 3):** a manual, low-commitment pilot testing whether seeing a real, achievable number changes behavior. New riders compared only against other new riders, on matched routes, shown an anonymized number rather than a named ranking — something like *"riders at your stage typically earn ₹X on a normal day."* Sent by SMS or rider inbox, built quickly against existing data — no new infrastructure, no production commitment. Run against a matched group of new riders who aren't shown anything, and compare ramp speed and income-gap trend between the two after a few weeks. If the shown group closes the gap faster, the credibility hypothesis holds and a fuller leaderboard is worth building properly. If it doesn't, or guardrails move — complaint rate, misdelivery, safety incidents — that's a real answer too, at a fraction of the cost of building the real thing first.

**Deliberately not shipping:** a permanent, pay-linked, public leaderboard in six weeks, before any of these tests has produced a signal.

---

## Q5 — What I'd actually say

> "You're right that new riders are underperforming and it's costing us, and I don't want to sit on that. But I think this is two separate problems wearing one hypothesis, and I don't think one leaderboard answers both.
>
> The 2 hours 40 minutes before a first delivery — I don't think that's a rider problem at all. Most of our riders are already paid per delivery, so slow mornings already cost them money directly, and that hasn't fixed it. My read is the real constraint is upstream — trucks and sorting, before a rider even checks in. Give me two weeks of timestamps across all 40 hubs and I'll have a clean answer either way.
>
> The new-rider gap is a fairer place to test your instinct, because there's a real version of it I think is right: new riders don't have anything to check their expected earnings against, they've only got what the recruiting agency told them. If they don't believe ₹1000 a day is real, they've got a reasonable reason to hold back. So let's test that directly, cheaply, before we build it: show a new-rider group a real, anonymized number — what someone at their stage typically earns — against a matched group who don't see it, and see if it moves how fast they ramp up. If it does, you'll have real evidence for building this properly. If it doesn't, we'll have learned that in two weeks instead of finding out after six.
>
> Two weeks from now, you'll have a hub answer, and the start of a real answer on the rider side — either a leaderboard design I can defend, or a better reason not to build one."

---

## Appendix — ramp speed, worked example

*Why ramp speed over a simple average productivity comparison:* an average collapses the timeline, so it can't distinguish a gap closing in three weeks from one stuck for three months — and it can't tell "everyone improving together" from "the cohort is really two populations, half converging and half stuck," which is exactly the failure mode a referral-farming or expectations problem would produce.

**Toy example — 10 riders hired the same week, orders/day, tracked to day 90:**

| Rider | Wk1 | Wk2 | Wk3 | Wk4 | Wk5 | Wk6 | ... | Wk12 | Outcome |
|---|---|---|---|---|---|---|---|---|---|
| R1 | 4 | 5 | 6 | 7 | **8** | 9 | ... | 11 | Converged wk5, held |
| R2 | 5 | 6 | **8** | 7 | 6 | 5 | ... | 5 | Converged wk3, then regressed |
| R3 | 5 | 6 | 7 | **8** | 9 | 10 | ... | 12 | Converged wk4, held |
| R4 | 4 | 5 | 6 | 6 | 5 | 4 | ... | 4 | Never converged, still active |
| R5 | 6 | 7 | **8** | 9 | 10 | 11 | ... | 13 | Converged wk3, held |
| R6 | 3 | 4 | churned | — | — | — | — | — | Churned wk2, pre-convergence |
| R7 | 5 | 6 | 7 | 6 | 5 | 4 | ... | 3 | Never converged, still active |
| R8 | 6 | **8** | churned | — | — | — | — | — | Converged wk2, churned wk3 |
| R9 | 6 | 7 | **8** | 9 | 10 | 11 | ... | 12 | Converged wk3, held |
| R10 | 3 | 4 | 5 | churned | — | — | — | — | Churned wk4, pre-convergence |

Tenured P10 floor (bold crossings above) = **8 orders/day**, from a separate 24-month-plus rider sample.

**Derived from the table:**

| Checkpoint | Active | Churned | Churn rate |
|---|---|---|---|
| W1 | 10 | 0 | 0% |
| W4 | 8 | 2 | 20% |
| W8 | 7 | 3 | 30% |
| W12 / D90 | 7 | 3 | 30% |

**Success rate (day 90):** converged and holding above floor = R1, R3, R5, R9 = **4/10 = 40%** — R2 excluded (regressed), R4/R7 excluded (never converged), R6/R8/R10 excluded (churned).

**Ramp speed**, successful riders only: weeks to first cross 8 → 5, 4, 3, 3 → **median 3.5 weeks**.
