# NYC Taxi Analytics: Power BI Dashboard Design

**Purpose:** let managers monitor revenue, demand, payments, locations and trip efficiency, and act on the recommendations in `report/insights_recommendations.md`.
**Audience:** operations and fleet managers (daily/weekly use), leadership (overview page).
**Pages:** 5 analysis pages plus 1 data-quality page.

| File | Use |
|---|---|
| `data/powerbi/taxi_trips.csv` | Data source: 6,370 trips × 36 columns (built by `notebooks/04_insights_and_dashboard_prep.ipynb`) |
| `report/powerbi/measures.dax` | Date table and all DAX measures |
| `report/powerbi/taxi_theme.json` | Color theme (colorblind-checked palette) |
| `report/powerbi/claude_desktop_guide.md` | Step-by-step: build this dashboard with Claude Desktop + the Power BI MCP server |

---

## 1. Setup (about 15 minutes)

1. **Get data → Text/CSV →** `taxi_trips.csv`. In Power Query, check the column types:
   - `pickup`, `dropoff` → Date/Time
   - `pickup_date` → Date
   - money, distance, duration and rate columns → Decimal
   - `*_order` columns and `pickup_hour` → Whole number
   - Rename the query to **Trips**.
2. **View → Themes → Browse for themes →** `taxi_theme.json`
3. **Modeling → New table →** paste the `Date` table from `measures.dax`. Mark it as a date table.
4. **Model view:** create the relationship `Date[Date]` (1) → `Trips[pickup_date]` (*), single direction.
5. **Sort by column:**
   - `Trips[pickup_day]` → `day_order`
   - `Date[Day Name]` → `Day Order`
   - `Trips[time_period]` → `time_period_order`
   - `Trips[distance_band]` → `distance_band_order`
6. **Create the measures**: paste `measures_query_view.dax` into DAX query view → Run → "Update model" (adds all 39 to the Trips table). Set their formats:
   - currency for revenue measures
   - percentage for `%` measures
   - 1 decimal place for duration and speed
7. **Fixed taxi colors:** wherever `color` is the legend, set **yellow = `#eda100`**, **green = `#008300`** under Format → Data colors.

> **Important rule for "per day" numbers.** For anything per day (e.g. *Trips per Day* by weekday), put **`Date[Day Name]`** or **`Date[Day Type]`** on the axis or slicer, not the Trips columns. March 2019 has 5 Fridays, Saturdays and Sundays but only 4 of each other weekday. The Date table divides by the right number of days, and the Trips columns can't.

---

## 2. Global slicers (synced across all pages)

Shown as a slim filter bar along the top of every page (**View → Sync slicers**):

| Slicer | Field | Type |
|---|---|---|
| Date range | `Date[Date]` | Between (slider) |
| Taxi type | `Trips[color]` | Buttons (Yellow / Green) |
| Payment | `Trips[payment]` | Dropdown |
| Pickup borough | `Trips[pickup_borough]` | Dropdown, multi-select |
| Day type | `Date[Day Type]` | Buttons (Weekday / Weekend) |

Add a **"Reset filters"** button (Insert → Buttons → Reset → Bookmark that clears all slicers).

---

## 3. Page layouts

Canvas: **16:9, 1280 × 720**. Every page has the same frame:

```
┌──────────────────────────────────────────────────────────────────────────┐
│ PAGE TITLE (dynamic)                          [Date] [Type] [Pay] [Boro] │  ← 60px header + slicers
├──────────────────────────────────────────────────────────────────────────┤
│  KPI cards row                                                           │  ← 110px
├──────────────────────────────────────────────────────────────────────────┤
│  Main visuals (2–4)                                                      │
└──────────────────────────────────────────────────────────────────────────┘
```

Left-edge navigation buttons: **Overview · Demand · Payments · Locations · Efficiency · Data Quality**.

---

### Page 1 — Executive Overview
*Question: how does the company generate revenue?* (Insights #1–4)

```
┌──────────────────────────────────────────────────────────────────────────┐
│ Net revenue $102,921 from 6,370 trips                                    │
├────────────┬────────────┬────────────┬────────────┬────────────┬────────┤
│ Net Revenue│Total Revenue│   Trips    │Avg Rev/Trip│Median/Trip │Airport │
│  $102.9K   │  $117.5K   │   6,370    │   $18.45   │   $14.16   │ Share %│
├────────────┴────────────┴─────────┬──┴────────────┴────────────┴────────┤
│ Revenue per day (line, by date)   │ Revenue composition                  │
│                                   │ (bar: fare/surcharges/tips/tolls)    │
├───────────────────────────────────┼──────────────────────────────────────┤
│ Trip share vs revenue share       │ Revenue by taxi type                 │
│ by distance band (clustered bar)  │ (bar: yellow / green)                │
└───────────────────────────────────┴──────────────────────────────────────┘
```

| Visual | Type | Fields |
|---|---|---|
| 6 KPI cards | Card (new) | `Net Revenue`, `Total Revenue`, `Trips Count`, `Avg Revenue per Trip`, `Median Revenue per Trip`, `Airport Revenue Share %` |
| Revenue per day | Line chart | X `Date[Date]`, Y `Net Revenue`; add a constant line at the average |
| Revenue composition | Bar (horizontal) | Values `Fare Revenue`, `Surcharges`, `Tips`, `Tolls` (use field parameters or 4 measures as series) |
| Trips vs revenue share | Clustered column | X `distance_band`, Y `Trip Share %` + `Revenue Share %` |
| Revenue by taxi type | Column | X `color`, Y `Total Revenue`; tooltip `Trips Count`, `Avg Revenue per Trip` |

---

### Page 2 — Demand & Time
*Question: when do customers need taxis the most?* (Insights #5–8)

```
┌──────────────────────────────────────────────────────────────────────────┐
│ Demand peaks at 18:00 · 205 trips per day                                │
├──────────────┬──────────────┬──────────────┬─────────────────────────────┤
│ Trips per Day│ Peak Hour    │Revenue per Day│ Weekend vs weekday trips/day│
├──────────────┴──────────────┴──────────────┴─────────────────────────────┤
│ Trips & avg revenue per trip by hour  (two stacked column charts,        │
│ same X axis 0–23 — never a dual axis)                                    │
├───────────────────────────────────┬──────────────────────────────────────┤
│ Day × hour heatmap (matrix,       │ Trips per day by weekday (column)    │
│ conditional-format background)    │ weekdays blue, weekend orange        │
└───────────────────────────────────┴──────────────────────────────────────┘
```

| Visual | Type | Fields |
|---|---|---|
| KPI cards | Card | `Trips per Day`, `Peak Hour Label`, `Revenue per Day` |
| Trips by hour | Column | X `pickup_hour`, Y `Trips Count`; conditional color: max = orange |
| Avg revenue by hour | Column (below, aligned) | X `pickup_hour`, Y `Avg Revenue per Trip` |
| Heatmap | Matrix | Rows `Date[Day Name]`, Columns `pickup_hour`, Values `Trips per Day`; Cell elements → Background color gradient `#f0efec` → `#0d366b` |
| Trips per weekday | Column | X `Date[Day Name]`, Y `Trips per Day`; color by `Date[Day Type]` |

---

### Page 3 — Payments & Tips
*Question: how do customers pay, and how much do they tip?* (Insights #9–10)

```
┌──────────────────────────────────────────────────────────────────────────┐
│ Payments & Tips                                                          │
├──────────────┬──────────────┬──────────────┬─────────────────────────────┤
│ Card Share % │Avg Tip (Card)│Median Tip %  │ No-Tip Rate (Card)          │
├──────────────┴──────────────┴──┬───────────┴─────────────────────────────┤
│ Avg revenue per trip by payment│ Tip % distribution (column, binned     │
│ (column)                       │ tip_pct, 5% bins, card only)            │
├────────────────────────────────┼─────────────────────────────────────────┤
│ Card share by taxi type (100%  │ Avg tip $ and median tip % by distance  │
│ stacked bar: card/cash)        │ band (two small column charts)          │
└────────────────────────────────┴─────────────────────────────────────────┘
```

| Visual | Type | Fields |
|---|---|---|
| KPI cards | Card | `Card Share %`, `Avg Tip (Card)`, `Median Tip % (Card)`, `No-Tip Rate (Card)` |
| Revenue by payment | Column | X `payment`, Y `Avg Revenue per Trip` |
| Tip % distribution | Column | X **tip_pct (bins)** (right-click `tip_pct` → New group → Bin size 5), Y `Trips Count`; visual filter `payment = credit card` |
| Card share by taxi type | 100% stacked bar | Y `color`, X `Trips Count`, Legend `payment` |
| Tips by distance | 2 × Column | X `distance_band`, Y `Avg Tip (Card)` / `Median Tip % (Card)` |

Add a text box on this page: *"Cash tips are not recorded. Tip figures cover card trips only."*

---

### Page 4 — Locations
*Question: where do the most valuable trips start and end?* (Insights #11–12)

```
┌──────────────────────────────────────────────────────────────────────────┐
│ Locations                                                                │
├──────────────┬──────────────┬──────────────┬─────────────────────────────┤
│Airport Rev   │Airport Share%│ Manhattan    │ Avg Rev/Trip (airport vs    │
│              │              │ trip share % │ non-airport)                │
├──────────────┴──────────────┴─┬────────────┴─────────────────────────────┤
│ Trips & revenue share by      │ Top 10 pickup zones by revenue           │
│ pickup borough (clustered bar)│ (table with data bars: trips, revenue,   │
│                               │  avg revenue per trip)                   │
├───────────────────────────────┼──────────────────────────────────────────┤
│ Zone volume vs value          │ Top routes (table: route, trips,         │
│ (scatter: trips × avg revenue)│  avg revenue; Top N = 10)                │
└───────────────────────────────┴──────────────────────────────────────────┘
```

| Visual | Type | Fields |
|---|---|---|
| Borough share | Clustered bar | Y `pickup_borough`, X `Trip Share %`, `Revenue Share %` |
| Top zones | Table | `pickup_zone`, `Trips Count`, `Total Revenue` (data bars), `Avg Revenue per Trip`; Filter Top N 10 by `Total Revenue` |
| Volume vs value | Scatter | Details `pickup_zone`, X `Trips Count`, Y `Avg Revenue per Trip`, Legend `is_airport_trip`; filter `Trips Count ≥ 20` |
| Top routes | Table | `route`, `Trips Count`, `Avg Revenue per Trip`, `Total Revenue`; Top N 10 by `Total Revenue` |
| Borough flows (optional) | Matrix | Rows `pickup_borough`, Columns `dropoff_borough`, Values `Trips Count`, background gradient |

> **No map visual.** The data has zone names but no coordinates, and Power BI geocodes NYC zone names unreliably. A map would need the NYC TLC taxi-zone shapefile loaded as a Shape Map. Treat that as a later enhancement.

---

### Page 5 — Efficiency
*Question: how efficiently are trips completed?* (Insight #13)

```
┌──────────────────────────────────────────────────────────────────────────┐
│ Efficiency                                                               │
├────────────┬────────────┬────────────┬────────────┬────────────┬─────────┤
│Avg Duration│Avg Distance│ Avg Speed  │ Rev / Mile │ Rev / Min  │Stuck in │
│  14.5 min  │  3.05 mi   │            │            │            │traffic  │
├────────────┴────────────┴────────────┴─┬──────────┴────────────┴─────────┤
│ Avg speed by hour (line)               │ Revenue per minute by hour (line)│
│ shade 8am–6pm as "congestion"          │                                  │
├────────────────────────────────────────┼──────────────────────────────────┤
│ Revenue per mile & per minute by       │ Longest / slowest trips (table,  │
│ distance band (two small columns)      │ sorted by speed asc)             │
└────────────────────────────────────────┴──────────────────────────────────┘
```

| Visual | Type | Fields |
|---|---|---|
| KPI cards | Card | `Avg Duration (min)`, `Avg Distance (mi)`, `Avg Speed (mph)`, `Revenue per Mile`, `Revenue per Minute`, `Traffic-Stuck Trips` |
| Speed by hour | Line | X `pickup_hour`, Y `Avg Speed (mph)` |
| Revenue/min by hour | Line | X `pickup_hour`, Y `Revenue per Minute` |
| By distance band | 2 × Column | X `distance_band`, Y `Revenue per Mile` / `Revenue per Minute` |
| Slow trips | Table | `pickup`, `route`, `distance`, `duration_min`, `speed_mph`, `total`; filter `duration_min > 30` |

---

### Page 6 — Data Quality (for analysts; can be hidden from managers)
| Visual | Fields |
|---|---|
| Cards | `Suspicious Fares`, `Fare Outliers`, count of `pickup_zone = "Unknown"` |
| Table | All trips where `data_quality_flag = "Suspicious fare"` |
| Text box | Cleaning summary from `02_data_cleaning` (62 impossible trips removed, missing values filled) |

---

## 4. Interactions & tooltips

- **Cross-filtering:** keep the default (click a bar → the page filters). Set **KPI cards to "None"** under Format → Edit interactions, so they react only to slicers. That way a click doesn't silently change the headline numbers.
- **Report tooltip page** (Page size: Tooltip, 320 × 240) showing `Trips Count`, `Total Revenue`, `Avg Revenue per Trip`, `Card Share %` and a small trips-by-hour column. Assign it to the zone, route and borough visuals.
- **Drill-through:** a hidden *Zone detail* page with drill-through field `pickup_zone`, showing hourly trips, top destinations and average values for that zone.

## 5. Design rules (follow on every page)

| Rule | Why |
|---|---|
| **Titles state the finding** (e.g. "Airports earn 3× more per trip"), using dynamic titles where numbers change | Readers get the message without studying the chart |
| **Never use a dual-axis chart.** Use two aligned charts instead | Two y-scales on one chart mislead readers |
| Theme palette only. Taxi type is always **yellow `#eda100` / green `#008300`** | Same entity = same color on every page |
| Orange (`#eb6834`) is used **only to highlight** the peak or a comparison series | Highlight keeps its meaning |
| Data labels on bars with ≤ 8 categories; none on dense charts | Yellow and other light colors fall below 3:1 contrast, so labels make values readable |
| Horizontal gridlines only, light gray `#e4e3df`; no borders or shadows | Keeps the data in front |
| Show **medians** next to averages for money and duration | Distributions are right-skewed |
| Units in every title or axis ($, min, mph, %) | No guessing |
| Alt text on every visual (Format → General → Alt text) | Accessibility / screen readers |

## 6. Values to check after building

Compare these numbers with no slicers applied:

| Measure | Expected |
|---|---|
| Trips Count | 6,370 |
| Total Revenue | $117,548 |
| Net Revenue | $102,921 |
| Avg Revenue per Trip | $18.45 |
| Card Share % | 71.4% |
| Peak Hour | 18:00 |
| Trips per Day (Wednesday) | 239.0 |
| Airport Revenue Share % | ~17% |

If **Trips per Day** by weekday shows small numbers (~23–36) instead of ~170–240, the visual is using `Trips[pickup_day]` instead of `Date[Day Name]`. The measure is then dividing by all 31 days.
