# Council Transcript — Used EV/PHEV Financing on the Lease Board

**Date:** 27 Jul 2026
**Question:** Should the Fall 2026 Lease Board include used EV/PHEVs for financing in addition to the current new-lease options?
**Verdict:** Exclude. Stay lease-only. Fix four rows, add a scope section, link a saved search.

---

## Verified Facts (confirmed by grep against `index.html`, treated as ground truth by the chairman)

- The data array has exactly **91 rows** (advisors variously said 90 and 94).
- `est:1` — price is an estimate — is set on **54 of 91 rows (59%)**.
- Exactly **two** rows carry a down payment in `disp`: Mazda CX-70 PHEV (`"$249 · $7,789 down"`) and Mazda CX-90 PHEV (`"$299 · $8,989 down"`).
- Exactly **two** rows are 39-month terms in a 36-month board: Porsche Macan Electric (`"$1,049 · 39mo"`) and BMW X5 xDrive50e (`"$929 · 39mo"`).
- The row schema has **no `asof` field and no `term` field**.
- "Not here on purpose" currently lists **only discontinued/unavailable models** — it is a vehicle-existence list, not a scope/methodology list.

---

## Framed Question

The artifact: a single self-contained hand-compiled `index.html` (~3MB, mostly base64 Wikimedia photos), no build step, no backend. Ranks NEW EV/PHEV SUVs leasable in West Hartford CT, fall 2026, on 36-month terms. Compiled 24 Jul 2026. The single organizing metric is $/mo; secondary axis is cargo cu ft indexed to a VW ID.4 baseline of 64.2. Federal EV credit $0 (expired 30 Sep 2025); the $4,000 federal *used* clean vehicle credit died the same day; CT CHEAPR is new-vehicle-only ($1,000 BEV / $500 PHEV, $50k cap, eligible list last refreshed Sep 2025). Realistic shopping zone $250–600/mo.

Tensions put to the council: comparability of a loan payment vs a lease payment in one sorted column; whether the used case still pencils with no incentives; steep post-credit used EV depreciation as the bull case; battery/warranty risk; data tractability of VIN-specific inventory in a static file; scope creep vs completeness.

---

## Advisor Responses

### The Contrarian

The comparability argument is already dead, and you should check the file before you make it. Your own board has `disp:"$249 · $7,789 down"` and `disp:"$299 · $8,989 down"` sitting in the same sorted column as zero-down offers. The CX-70 at $249 is really ~$465/mo. You also have 39-month terms in a 36-month board, and 54 of 91 prices are estimates. The column is *already* lying by more than a used-loan payment would lie. Defending lease-only on comparability grounds is defending a purity that doesn't exist. Fix the down-payment distortion first — that's a real bug shipping today, not a hypothetical one.

That said: **exclude used.** For a different reason than the one being offered.

The fatal flaw is temporal, not conceptual. Your CHEAPR eligible-list is stale since Sep 2025 — ten months — in a file where incentives are the *header*. If you can't refresh a dozen static incentive facts, you will not refresh VIN-level used inventory that turns over weekly. A stale lease row is a bad number. A stale used row is a car that was sold in April. That's a worse failure mode than omission.

Second: buy-vs-lease is decided once, before shopping — not by scanning row 47. Adding used rows answers a question the board isn't sequenced to ask.

Third, the bull case is a falling knife. The subvented new leases pulling used prices down are still being written *right now*; those returns hit the auction lanes in 2027-28. You'd absorb the depreciation the lessee is contractually immune to, on an asset whose worst-case repair (pack replacement) exceeds its own residual.

**"Not here on purpose" entry:** *Used EV/PHEV purchases. Different financial instrument, VIN-specific pricing, no federal used credit since 30 Sep 2025, no CHEAPR. A static file can't track it honestly.*

**Flip condition:** you decide you're buying, not leasing. Then this board is the wrong tool entirely — you need five VIN candidates with battery-health reports, not ninety rows.

### The First Principles Thinker

The question isn't "used or not." It's "what is this board's number actually measuring?" — and right now the answer is nothing coherent.

$/mo is not a primitive. It's a proxy for the real quantity: total cash out over a defined holding period, minus whatever asset value you hold at the end. The board already breaks on this internally. A CX-70 at $249 with $7,789 down is roughly $465/mo effective, and it sorts above vehicles that genuinely cost less. The comparability failure everyone fears from used financing is *already live* in the lease-only data. You don't have a used-vehicle problem. You have a metric problem, and used financing merely makes it visible.

Fix the metric first. One column — Effective 36-Month Cost: (down + fees + 36 × payment − end-of-term equity). For a lease, equity is zero by construction. For a 60-month loan on a used car, equity is estimated month-36 resale minus remaining principal. That single definition makes a used Ioniq 5 loan and a new Ioniq 5 lease the same kind of number, honestly, and it fixes the down-payment distortion you already have.

Second reframe: you are not choosing among 90 vehicles. You're choosing among three branches — lease new, buy used, keep what you have. This board is a beautifully optimized search over a sub-branch of branch one. Forty used rows degrade that search and still don't answer the branch question.

Verdict: no used rows. Ship the effective-cost column, then a separate small lease-vs-buy panel — 3-5 used nameplates, explicit residual assumption, stressed ±20%.

"Not here on purpose": used inventory is VIN-priced and hyper-local; a hand-compiled static file goes stale before the shopping trip.

Flips when: effective-cost is live and one used case beats the best comparable lease by more than your residual uncertainty. Then it's the same number, and it earns the row.

### The Expansionist

Include used. But not as a "used tab" — that's thinking too small.

The real opportunity: the board's organizing metric is wrong, and used is the forcing function that fixes it. `$/mo on 36 months` is not the thing being decided. The thing being decided is **total cost to have an EV in the driveway for three years.** Lease payment is one input to that. Add `down`, `msrp`, and a 36-month resale estimate to the data model and you can compute a single unified number — 36-Month Cost of Access = (payment × 36 + down + fees − equity at month 36) — that ranks a $249/mo CX-70 with $7,789 down *correctly* against a $38k used Ioniq 5 financed at 72 months. The comparability tension dissolves the moment you stop treating monthly payment as the metric and start treating it as a field. That upgrade makes the *existing 90 new rows* more honest too. The CX-70 at $249 is currently top of the board on a lie.

The undervalued asset here: 90 hand-compiled rows with cargo, CHEAPR, C&D, and photos already normalized. That's a *reusable frame*, not a shopping list. Used EVs are the second dataset that proves the frame generalizes — after that, off-lease returns, CPO, and 24-month leases all drop in for free.

Data tractability isn't the wall it looks like. Don't publish VINs. Publish **model-year price bands** — "2023 Ioniq 5 SEL, 30–45k mi, $26–31k" — with a `asof` date per row and a staleness badge. Bands survive weeks; VINs survive hours.

Scope-add: `deal: lease|finance`, `term`, `apr`, `resale36`, `battWarrantyRemaining`, `asof`. Six fields. Default filter stays lease-only, so today's board is unchanged for anyone who wants it.

### The Outsider

I'll start with what the name told me. It's a **Lease Board**. I opened it expecting leases. If I found a 72-month loan sitting in row 14 at $412/mo, I would assume that was a lease, because everything else is, and nothing on a table row shouts "different instrument." The single-number promise is exactly what makes the contamination invisible.

And you've already broken that number once. The $249 CX-70 has $7,789 down. That's $465/mo in real money. You knew, so you patched it into a `disp` string — a footnote a stranger's eye slides right past while the sort column happily ranks it #1. If the metric can't honestly hold two *leases*, it cannot hold a lease and a loan. Adding used doesn't introduce the comparability problem; it makes the existing one unfixable.

The jargon tell: "residual," "disposition fee," "equity." None of that appears on the board because you already know it. The board silently assumes its reader understands that a lease payment rents depreciation and a loan payment buys a car. A used row would require you to teach that in a tooltip. If a decision tool needs a tutorial to keep its main column from lying, the column is wrong.

Also — you're buying one car, once, this fall. Ninety rows is already generous. Used inventory isn't rows, it's a search that's stale by Saturday. A file compiled 24 Jul cannot serve it.

**Exclude.** "Not here on purpose": *Used EVs and financed purchases. The board ranks one thing — the monthly cost of renting a new EV for 36 months. A loan payment is not that number.*

The flip condition isn't depreciation getting steeper. It's you answering "will I keep this past three years or exceed the mileage cap?" with yes. If so, don't add rows — this is the wrong board, and you need a different one.

### The Executor

No. Keep it lease-only. Here's why, in Monday-morning terms.

You have 94 rows in a hand-typed JS array. Each new-lease row is one number that's true for a month and true for everyone in Connecticut. A used row is not a row — it's a VIN. To publish "used Ioniq 5, $340/mo" you need a specific car at a specific dealer with specific mileage, and the moment that car sells, your board is lying. You'd be re-scraping and re-hand-editing a 3MB static file every week. You won't. It'll go stale in three weeks and then you'll distrust the whole board, including the lease side that was fine.

Also: the comparable-cost metric doesn't exist. A 72-month note at $340 and a 36-month lease at $340 are not the same number, and any "true cost of ownership" column you invent requires you to guess a residual value in the most volatile used-EV market in a decade. You'd be shipping a made-up number in the one column the entire tool is organized around. That poisons the asset.

What to do instead, this week, in 20 minutes: add one line to "Not here on purpose" — *"Used EV/PHEV purchase financing. Inventory is VIN-specific and changes daily; a static board can't stay honest about it. The $4,000 federal used credit expired 30 Sep 2025 and CHEAPR is new-only, so there's no incentive story to model."* Then add a link to a saved CarGurus/AutoTrader search with your filters pre-set. That's the used market, live, zero maintenance, and it's the right container.

Flip condition: a used federal credit comes back, or you find yourself checking used listings more than twice a week. Then build it as a separate page with a live feed — never as rows in this table.

---

## Peer Reviews

**Anonymization mapping:** A = Expansionist · B = Contrarian · C = Outsider · D = Executor · E = First Principles

Score: **B strongest 4/5, E strongest 1/5. A named biggest blind spot 5/5.**

### Review 1

I checked the file. 91 rows, `est:1` on exactly 54 — B's "54 of 91" is verified, not asserted. Only two rows carry down payments in `disp` ($7,789 CX-70, $8,989).

1. **Strongest: B.** Only response whose numbers survive contact with the file. It separates the real shipping bug (down-payment distortion) from the decision, then excludes on a temporal argument the others don't make: a CHEAPR list stale ten months proves refresh capacity, and stale used rows fail worse than stale lease rows. Weak point: its flip condition ("you decide you're buying") is circular.
2. **Biggest blind spot: A.** It calls `resale36` one of "six fields," but that field is a hand-guessed residual in the most volatile used-EV market in a decade, times 91 rows, maintained manually. A ships a fabricated number into the sort column — the exact failure D names. Its "reusable frame" reasoning optimizes for a product that doesn't exist; the user buys one car once.
3. **All five missed:** the bridge already on the board — the lease-end purchase option. Every listed lease has a contractual buyout at residual. That's used-EV ownership, priced, non-VIN, and static. Also: A, C, and E generalize a two-row `disp` artifact into a systemic metric failure. Fix two rows, not the architecture.

### Review 2

1. **Strongest: E.** It's the only response that both diagnoses the metric failure (like A) and refuses the scope-add (like B/C/D), then gives a falsifiable flip condition tied to the fixed metric rather than to mood. B is a close second — best empirical grounding — but its third argument (absorbing depreciation, pack-replacement risk) is speculation dressed as analysis.
2. **Biggest blind spot: A.** It calls the maintenance burden away. `resale36` must be hand-typed for all 91 rows in a file where 54 of 91 prices are already `est:1` guesses — A would stack a speculative residual on top of a speculative price and rank on the product. And "the frame generalizes" is asserted against a git history of two commits in one day. A confirms B's argument while claiming to refute it.
3. **All five missed:** the schema has no temporal field at all. Row shape has no `asof`, no `term` (so B's real 39-month finding isn't even expressible). Every advisor argued staleness; none noticed lease rows are already unfalsifiable about age, and lease programs reset monthly. Also: "Not here on purpose" currently lists discontinued models only. B/C/D/E all propose dropping a financial-instrument exclusion into a vehicle-existence list. Wrong container — it needs its own heading.

### Review 3

1. **Strongest: B.** It's the only one that verified the file before arguing. The $7,789-down claim checks out, and B alone names the decisive asymmetry: the CHEAPR/incentive facts have already been stale for ten months. Someone who won't refresh a dozen static facts will not refresh weekly inventory. That's an evidence-based capacity argument, not a taste argument. C reaches the same verdict on genre-integrity grounds, which is thinner.
2. **Biggest blind spot: A.** It proposes `resale36` as the unifying field — a 36-month resale estimate for used EVs in the most volatile residual market in a decade — and calls the comparability tension "dissolved." It isn't; it's relocated into a guessed number that now drives the sort for all 91 rows. D names exactly this. A also never says where the used price bands come from or who maintains them.
3. **All five missed:** the board is for one buyer making one purchase this fall. Nobody asked the cheapest question — spend an hour on used listings, and if nothing beats the lease board, the answer is settled without touching the file. Also unexamined: 54 of 91 prices are already estimates, so the lease side isn't the trustworthy baseline everyone assumed.

### Review 4

1. **Strongest: B.** The only response verifiably grounded in the file that still reaches a decision. I checked: both down-payment strings are literally in the file; there are 91 `disp:` rows (not D's 94); two rows are `39mo`; `est:1` is set on exactly 54 of 91 — B's number, exact. B then argues against its own opening and lands on exclusion for a harder reason: staleness. The 10-month-stale CHEAPR list is the tell — a maintenance-capacity argument backed by observed behavior beats a principled one.
2. **Biggest blind spot: A.** It proposes six new fields and a resale estimate for a maintainer who has not refreshed a dozen static incentive facts since Sep 2025. "Bands survive weeks" is asserted, not shown, and `resale36` is a guessed number in the sort column — exactly what D names as poisoning the asset. A treats 91 hand-typed rows as a "reusable frame"; it is a one-time purchase aid with a deadline.
3. **All five missed:** the board's expiry. This is a Fall 2026 decision for one car. Every response optimizes an artifact that becomes worthless in ~8 weeks, so metric-refactor proposals (A, E) cost more than the decision is worth. Also unexamined: 91 rows is itself the defect — none asked whether the board should shrink. And nobody flagged that `est:1` on 59% of rows already undermines the ranking more than any used row would.

### Review 5

1. **Strongest: B.** The only response that opened the file before arguing, and its claims verify: exactly 2 rows carry down payments, 54 of 91 flagged `est:1`, 39-month terms present. It concedes the weak argument (comparability purity) before opponents can spend it, then lands a reason no one else reaches — the compiler's revealed maintenance capacity. CHEAPR stale ten months is observed behavior, not speculation. A stale lease row is a bad number; a stale used row is a sold car.
2. **Biggest blind spot: A.** Its unified "36-Month Cost of Access" needs a `resale36` guess on all 90 new rows — an invented residual, in the most volatile used-EV market in a decade, installed as the primary sort key — and calls this more honest. It trades two known-wrong displays for ninety fabricated ones, and promises "six fields" without naming who refreshes them.
3. **All five missed:** the down-payment distortion is 2 rows out of 91, not a metric failure. A, C, and E built theses on "the metric already lies." True — and it's a data-entry fix: display $465 and $549 effective. No new column earns its keep. Meanwhile the actual credibility threat, 54 estimated prices with wider error bars than any lease-vs-loan gap, went unaddressed by everyone.

---

## Chairman's Verdict

### Where the Council Agrees

Four of five advisors — and every peer reviewer — land on **exclude**. The convergence isn't taste; it's three independent arguments arriving at the same place:

**1. Maintenance capacity is the binding constraint, and it's already been measured.** The CHEAPR eligible list has been stale since Sep 2025 — ten months — in a file whose header is incentives. That's not a hypothesis about whether you'll maintain used rows; it's observed behavior on a dozen static facts that never move. Used inventory moves weekly. A stale lease row is a wrong number you can sanity-check; a stale used row is a car that was sold in April.

**2. The failure mode is asymmetric.** Omission costs you one click to CarGurus. Inclusion costs you a board you stop trusting — including the lease side, which is the part that works.

**3. The board's metric is doing less work than it appears to.** Every advisor noticed. `$/mo` already carries a $249 CX-70 with $7,789 down (~$465 effective) sorting above genuinely cheaper cars, plus two 39-month terms in a 36-month board, plus 54 of 91 prices flagged `est:1`. Nobody disputes the diagnosis. They dispute what follows from it.

**4. If the real question is "buy or lease," this board is the wrong instrument entirely.** The Contrarian, the Outsider, and First Principles all say it independently: that branch is decided before you scan row 47, and the answer isn't forty more rows — it's five VINs with battery-health reports.

### Where the Council Clashes

**The one real fight: is the down-payment distortion a metric failure or a data-entry bug?**

The Expansionist and First Principles argue it's structural. Monthly payment is a proxy, not a primitive; the honest quantity is total cash out over the holding period minus end equity. Build `Effective 36-Month Cost` and the lease/loan comparability problem dissolves — used rows become the same kind of number, and the existing rows get more honest too.

The Executor, the Contrarian, and three reviewers say that's a two-row artifact being generalized into an architecture. The fix is arithmetic, not a column: display $465 and $549. Reviewer 5 puts the trade sharply — the unified metric needs a `resale36` guess on all 91 rows, in the most volatile used-EV residual market in a decade, **installed as the primary sort key**. You'd trade two known-wrong displays for ninety fabricated ones, stacked on top of 54 prices that are already estimates. That's a speculative residual multiplied by a speculative price, and you'd rank on the product.

Reasonable people split here because both sides are right about different things. The metric *is* conceptually wrong. But the elaborate fix imports more error than the flaw it removes — and it's proposed to a maintainer whose demonstrated refresh rate is zero over ten months.

**Second clash: is `est:1` on 59% of rows disqualifying?** Reviewers 3, 4, and 5 flag it as the real credibility threat that all five advisors ignored. The counter: an estimated lease payment for a nameplate is wrong by tens of dollars; a stale used listing is wrong by *existing*. Different orders of failure. The estimates argue against adding precision-theater on top, not against the board.

### Blind Spots the Council Caught

**The bridge nobody saw (Reviewer 1).** Every lease on the board already has a contractual buyout at residual. That *is* priced used-EV ownership — non-VIN, static, model-level, needs no scraping. If used ownership genuinely deserves representation, it's already latent in the data and costs one note, not a dataset.

**The schema has no temporal field at all (Reviewer 2).** No `asof`, no `term`. Every advisor argued about staleness; none noticed the existing lease rows are *already unfalsifiable about age* — and lease programs reset monthly. B's own 39-month finding isn't even expressible in the row shape. Staleness isn't a used-vehicle problem the board would acquire. It's a problem the board already has and can't display.

**Wrong container for the exclusion (Reviewer 2).** "Not here on purpose" is currently a discontinued-vehicle list — Acura ZDX, Nissan Ariya, dead Stellantis PHEVs. Four advisors propose dropping a financial-instrument exclusion into it. That's a category error; it needs its own heading.

**The board expires in about eight weeks (Reviewer 4).** This is one buyer, one car, this fall. Every metric-refactor proposal costs more than the decision it serves. And nobody asked the cheapest question (Reviewer 3): spend one hour on used listings. If nothing beats the lease board, the question is closed without touching the file.

**The Expansionist's frame argument doesn't survive the git log (Reviewer 2).** "Reusable frame, generalizes to off-lease returns, CPO, 24-month leases" — asserted against a repository with two commits in one day. It's a purchase aid with a deadline, not a platform.

### The Recommendation

**Stay lease-only. Do not add used rows, and do not build the unified cost metric.**

The Contrarian's argument wins on evidence: the ten-month-stale CHEAPR list is a *measurement* of maintenance capacity, not a guess about it, and used inventory demands roughly 50x that refresh rate. Everything downstream follows.

The Executor's version of the fix wins on cost: one line of scope documentation plus a saved CarGurus/AutoTrader search with your filters pre-set. That's the used market, live, at zero maintenance, in the right container. Twenty minutes.

Reject the Expansionist's six fields. `resale36` is a hand-typed guess in the volatile residual market, installed as the sort key, maintained by someone with a demonstrated refresh rate of zero — it converts a two-row display bug into a ninety-row fabrication and calls that honesty. Reject the elaborate half of First Principles too, for the same reason: the effective-cost column is correct in theory and unaffordable in practice at eight weeks to decision.

But the Outsider is right that the current column lies, so fix it the cheap way — as data entry, not architecture. Rewrite the two `disp` strings to lead with the effective number: `"$465 eff · $249 + $7,789 down"` and `"$549 eff · $299 + $8,989 down"`, and set `price` to 465 and 549 so the **sort** stops lying. Same for the two 39-month rows — note the term in `disp` so a stranger's eye catches it. Four rows, ten minutes, no schema change.

Then the "Not here on purpose" section gets a second heading — **"Scope, on purpose"** — separate from the discontinued-vehicle list, containing:

> **Used EV/PHEV purchase financing.** This board ranks one thing: the monthly cost of renting a new EV for 36 months. A loan payment is not that number. Used pricing is VIN-specific and turns over weekly; a hand-compiled static file can't stay honest about it. The $4,000 federal used clean vehicle credit expired 30 Sep 2025 and CHEAPR is new-vehicle-only, so there is no incentive story to model. For the used market, use the saved search →

Add one more line, because Reviewer 1 found it and it costs nothing: every lease here has a contractual buyout at residual. If you want to own one of these cars, the path runs through the lease, not around it.

**Flip condition — the Executor's, not the Contrarian's circular one:** a federal used-EV credit returns, *or* you catch yourself checking used listings more than twice a week. Either way, the answer is a separate page with a live feed. Never rows in this table.

### The One Thing to Do First

Before touching the file: spend one hour on a CarGurus search for used Ioniq 5 / ID.4 / Mach-E in a 100-mile radius around West Hartford. If nothing there beats the best comparable lease on the board by a margin you'd actually act on, the question is settled empirically — and you'll have the saved-search URL you need for the "Scope, on purpose" entry anyway.
