# NYC Taxi Analytics: Insights & Recommendations

**Data:** 6,371 cleaned taxi trips, March 2019 (a sample of NYC yellow and green taxi trips).
**Source analysis:** `notebooks/03_eda.ipynb` · Charts: `report/figures/`

**Revenue definitions:**
- **Total revenue** = everything the passenger paid: $117,555
- **Net revenue** = fare + surcharges, leaving out tips (which go to drivers) and tolls (which are passed through): **$102,928**

Priority: **High** = large revenue impact or quick to act on · **Medium** = clear benefit, needs planning · **Low** = supporting or data quality.

## Insights & recommendations

| # | Area | Insight (evidence) | Recommendation | Priority | KPI to track |
|---|---|---|---|---|---|
| 1 | Revenue | Metered **fares are 70.5%** of total revenue. Tips (10.7%) and tolls (1.8%) are not company income, so "total" overstates what the company earns by ~$14.6k. | Report **net revenue (fare + surcharges)** as the headline KPI, with total revenue shown separately. | High | Net revenue; Net revenue per trip |
| 2 | Revenue | **Long trips (10+ mi) are 6% of trips but 20% of revenue** ($58.50/trip vs $11.70 for trips under 2 mi). The **top 10% of trips earn 28%** of revenue. | Protect long-trip supply: make sure cabs are available for airport and outer-borough requests, and don't let drivers refuse long trips. | High | Revenue share of 10+ mi trips; Avg revenue per trip |
| 3 | Pricing | Fare follows distance closely (**≈ $4.45 + $2.81/mile, R² = 0.92**). The **$52 JFK flat fare** earns less than the meter would on trips longer than ~17 mi. | Monitor flat-fare trips separately. Model the revenue effect of the flat fare on long JFK trips and feed it into pricing discussions with the regulator. | Medium | Revenue per mile (flat-fare trips vs metered) |
| 4 | Fleet | **Yellow cabs earn 86% of revenue** (5.7× the trips of green). Green trips are slightly longer but earn less ($16.61 vs $18.78), mostly because of **lower recorded tips ($0.82 vs $2.18)**. **40% of green trips are paid in cash** vs 26% for yellow. | Increase card and contactless use in green cabs (in-cab promotion, card-first payment screens). This makes tips visible and pushes green trips toward yellow's per-trip value. | Medium | Card share by taxi color; Avg revenue per trip (green) |
| 5 | Demand | **Busiest and highest-revenue period: 5–9 pm, peaking at 6 pm** (416 trips, $7.5k). It is strongest **Wed–Fri**; the single busiest slot is **Thursday at 6 pm**. | Schedule maximum fleet on weekday evenings, Wed–Fri especially. Avoid driver shift changes during 5–7 pm. | High | Trips per hour (peak); Revenue per hour |
| 6 | Demand | **4–5 am has the lowest demand (~51–56 trips) but the highest value per trip ($22–24)**. These are longer trips, often to the airports. | Keep a small but guaranteed early-morning fleet, focused on airport runs, rather than cutting coverage entirely. | Medium | Avg revenue per trip by hour |
| 7 | Demand | **Weekends have no morning rush, but late-night demand is 2–5× higher** than on weekdays (midnight to 2 am). Weekday demand is ~11% higher overall (213 vs 190 trips/day). | Run a dedicated **weekend late-night shift** (Fri/Sat 10 pm–3 am) and a lighter weekend morning shift. | High | Trips per day by day type; Weekend night trips |
| 8 | Demand | **Sunday (173 trips/day) and Monday (176) are the quietest days**, vs Wednesday at 239. | Schedule vehicle maintenance, training and driver days off on **Sunday and Monday**. | Low | Trips per day by weekday |
| 9 | Payments | **71% of trips are paid by card**. Card trips are worth **$19.93 vs $14.74 for cash** (+35%), and are still ~$2.40 higher with tips excluded. Cash tips are never recorded. | Promote card and contactless payment. Consider a way to record cash tips (driver app entry) so tipping data is complete. | Medium | Card payment share; Avg revenue per trip by payment |
| 10 | Tipping | Card customers tip a **median 25.6%** of the fare, clustered at the **20/25/30% preset buttons**. Tip % falls with distance (29% → 20%), while tip $ rises ($1.92 → $6.88). **9.7% of card trips have no tip.** | Keep the preset tip options. Test fare-dependent presets, such as a fixed $ amount for long trips, to lift tips on long trips. | Low | Median tip %; Avg tip $ by distance band |
| 11 | Locations | **JFK ($8.2k) and LaGuardia ($6.3k) are the top revenue pickup zones**, with average trips worth **$56 and $43**, 3× a typical Manhattan trip. Queens is 10% of trips but 17% of revenue. | Keep a **steady supply of cabs at airport taxi ranks**, especially at evening arrival peaks. Use the airport ↔ Midtown corridor as a priority dispatch route. | High | Airport trip revenue; Airport share of revenue |
| 12 | Locations | **Busiest zones (Midtown Center, Upper East Side, Penn Station) generate short, cheap trips** (~$14–18). Zone volume and value per trip are barely related (ρ = 0.16). **76% of trips stay within Manhattan.** | Treat Manhattan as the **volume base**: focus on fast turnaround and short waits between trips there, not on value per trip. | Medium | Trips by zone; Avg revenue per trip by zone |
| 13 | Efficiency | Traffic averages **~9 mph from 8 am to 6 pm** (8.7 mph at 4 pm). Revenue per minute drops to **$1.22–1.30** in the daytime vs **$1.85** at 4–5 am. **15 trips crawled at 2–4 mph** through Midtown. | Use real-time traffic data in dispatch to avoid sending cabs into Midtown during the daytime. Include time-of-day congestion in driver earnings and pricing reviews. | Medium | Revenue per minute by hour; Avg speed by hour |
| 14 | Data quality | **62 impossible trips removed** (zero distance, zero/negative duration, >60 mph). **94 trips had 0 passengers.** 44 trips are missing a payment type and 26–45 are missing zones. **4 trips have suspicious fares**, e.g. $52 for 0.11 mi, or $1.30 for 3.1 mi. | Add validation at the meter/data-entry stage (block 0-distance fares, require passenger count). Review the flagged trips for meter faults or fraud. | Low | % of trips failing validation |

## Top 5 actions

1. **Make net revenue the headline KPI** (#1).
2. **Put maximum fleet on weekday evenings, 5–9 pm, Wed–Fri** (#5).
3. **Keep cabs supplied at the airports**, the highest-value pickup locations (#11, #2).
4. **Add a weekend late-night shift** (#7).
5. **Push card payments, especially in green cabs** (#4, #9).

## Limitations
- One month (March 2019) of **sample** data. Counts show relative, not absolute, demand, and there is no seasonality.
- Cash tips are not recorded, so tip comparisons are limited to card trips.
- There is no cost data (fuel, driver pay), so the analysis covers **revenue, not profit**.
