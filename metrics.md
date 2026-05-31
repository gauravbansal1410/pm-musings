# North Star Metrics — Dating Apps (Hinge, Tinder, Bumble)
> PM strategy discussion notes

---

## 1. What is Hinge's North Star?

- Hinge's official North Star (confirmed publicly by Match Group CEO Spencer Rascoff): **"getting users on more great dates"**
- Also embedded in Hinge job postings: *"our success is defined by one simple metric — setting up great dates"*
- Fits their core brand positioning: **"designed to be deleted"**
- Mission and monetization are aligned — users who go on great dates refer friends, which drives new subscribers (word-of-mouth flywheel)

### Key candidate metrics considered (and why "dates" wins)
| Metric | Why it falls short |
|---|---|
| MAU | High MAU on a dating app = people *not* finding relationships. Optimising this works against the mission. |
| Matches per user | Shallow proxy. Gaming the algorithm inflates it. Most matches go nowhere. |
| Conversations per user | Better, but still a leading indicator. People can chat endlessly without meeting. |
| **Dates facilitated** ✅ | Maps directly to user value. Aligns mission with revenue. Creates correct product incentives. |
| Relationships started | Purest signal but hardest to measure — not viable as primary North Star. |

---

## 2. How Hinge Measures "Dates" — It's Triangulated

The measurement is not purely survey-based. Three signals are combined:

1. **Phone number exchange (passive, in-app):** Hinge detects 10-digit numbers shared in chat via regex scanning. Users who exchange numbers meet IRL an average of **4 days later** (established retrospectively from millions of data points).
2. **"We Met" prompt (triggered survey):** Fires automatically ~4 days after number exchange. Asks if they met, and if they'd see the person again. Launched **October 2018** — not a new feature, 7+ years of data maturity.
3. **Deletion + inactivity patterns:** App inactivity and account deletion are used alongside "We Met" data to infer which matches became relationships.

### Beta data on "We Met"
- 90% of users said their first date was great
- 72% said they wanted a second date
- Hinge was claiming **50,000+ dates/week** (and later a "date every 3 seconds") — primarily measured via phone number exchanges

---

## 3. Should "Hard to Measure" Metrics Be a North Star?

**Yes — and this is the more sophisticated PM philosophy.**

- The alternative (picking MAU or swipes because they're easy) is more dangerous — you end up optimising for a clean but wrong metric
- The real PM skill is **building measurement infrastructure** to make the hard metric trackable
- Hinge solved this by layering passive behavioral signals (number exchange) with triggered surveys ("We Met") and inactivity signals
- Former Hinge head of product: *"engagement numbers don't always mean success"*
- Justin McLeod (Hinge CEO): *"When we help people get on more dates, we grow faster, and people tell their friends more"*

---

## 4. Global Applicability — The India Problem

The "phone number in chat = date proxy" breaks in several markets, particularly India:

| Assumption (US/UK) | Reality in India |
|---|---|
| Numbers shared by typing digits in chat | Numbers exchanged via WhatsApp invite or verbally — invisible to Hinge's regex scanner |
| 4-day window to date | Longer courtship arc (weeks to months) due to cultural norms, family considerations, safety concerns |
| Phone number = off-app intent | Instagram handle is the preferred "safe handoff" — Hinge can't detect this |
| Women share numbers readily | Safety concerns mean lower conversion rate from match → number exchange |
| "Date" = casual coffee in 4 days | Indian urban "date" is more planned/formal; Tier 2/3 cities even more so |

### What Hinge should track instead in India
- Instagram handle shares or "WhatsApp me" message patterns (as proxy for off-app handoff)
- Longer trigger window for "We Met" (10–14 days vs 4)
- Conversation depth (15+ messages) + app inactivity as composite signals
- **The North Star stays the same — "dates facilitated" — but measurement instrumentation needs localisation**

---

## 5. Tinder vs Bumble vs Hinge — Different North Stars

> Same app category. Three completely different North Stars. Because North Star is downstream of **business model**, not just product category.

### The Key Question: Does revenue go up when users succeed?

| App | Mission ↔ Money Alignment | Why |
|---|---|---|
| **Hinge** | ✅ Fully aligned | User success → word-of-mouth → new subscribers. Revenue and mission point in the same direction. |
| **Bumble** | ⚠️ Partial | Dating success = dating churn, but BFF/Bizz modes can retain users. Multi-mode is the hedge. |
| **Tinder** | ❌ Misaligned | A user who finds a relationship and deletes = lost revenue. Optimal Tinder user keeps swiping and paying. |

### Recommended North Stars by app

**Hinge** → `Great dates per month`
- Confirmed by CEO + job postings
- Measured via phone number exchange + "We Met" + deletion signals

**Tinder** → `% of users converting to paid` (or engagement-based metric)
- ~$2B revenue, ~75M MAU, flat growth
- Business model requires engagement loops, not successful exits
- Former Tinder CPO (Ravi Mehta) explicitly wrote: *"there is no single metric that can measure [Tinder's] strategy"* — they use a North Star **strategy**, not a metric
- Tinder concentrates on % of paid accounts rather than absolute MAU

**Bumble** → `Quality connections per active woman`
- Women are Bumble's supply side — their engagement determines whether men find it worth paying for
- ~$1B revenue (declining), multi-mode platform (Date + BFF + Bizz)
- If women stay engaged and find quality connections, men subscribe → revenue flows → mission served

---

## 6. Core Framework: How to Derive a North Star

Three inputs determine the right North Star:
```
Mission / Positioning + Business Model (how you make money) + User Intent (what users actually want) = North Star Metric
```
The metric you optimise for *becomes* the product you build. Picking the wrong one (because it's easy to measure) is a slow product death.

---

*Sources: Match Group earnings calls, Hinge CEO interviews, TechCrunch (2017, 2018), Mixpanel blog, Future.com North Star Metrics, Ravi Mehta (former Tinder CPO) blog post*
