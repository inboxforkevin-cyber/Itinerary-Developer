# Workflow: Itinerary Developer

## Objective
Generate personalized travel destination recommendations and a day-by-day itinerary for individuals, couples, friend groups, or families — either suggesting destinations or building around a known one.

---

## Step 1: Collect User Inputs

Ask the user for the following. All fields are required before proceeding.

| Input | Options / Notes |
|---|---|
| **Travelers** | Solo, couple, friend group, family (with ages if family) |
| **Budget** | Total trip budget (USD or specify currency) |
| **Trip length** | Number of days |
| **Travel style** | One or more: Honeymoon, Adventure, Food, Nightlife, Culture, Relaxation, Business |
| **Departure airport/city** | Used to estimate flight costs and filter reachable destinations |
| **Desired travel month** | Affects pricing, weather, and availability |
| **Destination known?** | Yes (user specifies) / No (AI recommends) |

If the destination is unknown, proceed to Step 2. If known, skip to Step 3.

---

## Step 2: Destination Recommendations (if no destination specified)

Use the collected inputs to surface 3–5 destination options. For each destination, produce a recommendation card containing:

- **Destination name** (city + country)
- **Why it fits** — 2–3 sentence explanation tied directly to the user's travel style, group type, and budget
- **Estimated round-trip flight cost** from departure city (economy, per person)
- **Best time to visit** — confirm whether the user's desired month is ideal, shoulder, or off-season
- **Estimated daily spend** (accommodation + meals + activities, per person)
- **Highlight activities** — 3 bullet points representative of the destination

Filter candidates by:
- Budget feasibility (flight + stay + activities should fit within total budget)
- Weather suitability for the desired month
- Fit with travel style(s)

**Edge cases:**
- Budget too low for any international destination → suggest domestic alternatives or flag the constraint and ask if budget is flexible
- Month is peak season for desired style (e.g., ski trip in summer) → surface the mismatch and recommend alternatives

---

## Step 3: Activity Recommendations

Once destination is confirmed, generate a list of recommended activities. Each activity card must include:

- **Activity name**
- **Type** — Food, Cultural, Adventure, Nightlife, Relaxation, Sightseeing, etc.
- **Price tier** — Budget / Mid-range / Luxury (with estimated cost per person)
- **Location name + website** (if applicable)
- **"Why this fits you"** — one sentence tied to the user's travel style and group type

Present activities organized by **type** first, then sortable by **price tier**.

**Per-activity budget customization:** Prompt the user: *"Some activities can be mix-and-matched across budget levels — do you want to specify a price tier for any particular type (e.g., splurge on food but keep sightseeing budget)?"* Adjust recommendations accordingly.

**Minimum activity count:** At least 2 options per price tier per major type relevant to the user's travel style.

**Edge cases:**
- Activity type has no budget options (e.g., deep-sea diving is inherently expensive) → note this and suggest the closest affordable alternative
- Family with young children → exclude nightlife; flag age-inappropriate activities

---

## Step 4: Hotel Recommendations

Generate 3–6 hotel recommendations for the destination. Each hotel card must include:

- **Hotel name**
- **Price tier** — Budget / Mid-range / Luxury
- **Estimated nightly rate** (per room, in USD)
- **Location relative to key attractions** (e.g., "walkable to the Old Town")
- **Best for** — who this hotel suits (couples, families, solo travelers, etc.)
- **Booking link or website** (if known)

Provide at least one option per price tier.

**Edge cases:**
- Destination has limited budget accommodation → flag this and recommend nearby towns or alternative areas
- Family group requires multi-room or suite → filter for family-friendly properties and note room configuration

---

## Step 5: Day-by-Day Itinerary

Build a structured itinerary covering every day of the trip. Format:

```
Day N — [Theme or destination area]

Morning:   [Activity] — [Est. cost/person] — [Notes]
Afternoon: [Activity] — [Est. cost/person] — [Notes]
Evening:   [Activity or meal] — [Est. cost/person] — [Notes]

Daily spend estimate: $X–$Y per person
```

Rules:
- Balance activity types across the trip (don't cluster all food activities on one day)
- Front-load major sights earlier in the trip (energy is higher)
- Include at least one rest/flex half-day for trips of 5+ days
- Account for travel day(s) — arrival/departure days get lighter schedules
- Reference activities from Step 3 where possible; add new ones as needed for completeness

**Edge cases:**
- Trip is 1–2 days → compress to morning/afternoon/evening blocks only, no rest day
- Business travel style → include coworking spaces or hotel business center options; keep evenings lighter

---

## Step 6: Budget Summary

Produce a total estimated cost breakdown:

| Category | Budget Tier | Mid-Range Tier | Luxury Tier |
|---|---|---|---|
| Flights (round-trip, per person) | | | |
| Hotel (total stay) | | | |
| Activities (total) | | | |
| Food & Drink (total) | | | |
| Local Transport | | | |
| **Total per person** | | | |
| **Total for group** | | | |

Flag if any tier exceeds the user's stated budget, and note which category is the biggest driver.

---

## Tools to Use

| Task | Tool (when available) |
|---|---|
| Flight cost estimates | `tools/flight_search.py` |
| Hotel search | `tools/hotel_search.py` |
| Activity lookup | `tools/activity_search.py` |
| Weather/seasonality check | `tools/weather_check.py` |
| Itinerary export (Google Sheets/Docs) | `tools/export_itinerary.py` |

If a tool doesn't exist yet, perform the task directly using web search or API calls, then note that a tool should be built for future runs.

---

## Output Deliverables

1. **Destination recommendation cards** (Step 2, if applicable)
2. **Activity cards** organized by type and price tier (Step 3)
3. **Hotel recommendation cards** by price tier (Step 4)
4. **Day-by-day itinerary** (Step 5)
5. **Budget summary table** (Step 6)

Deliverables are exported to Google Sheets or Docs when tools are available. Otherwise, present inline and offer to export.

---

## General Edge Cases

- User changes destination mid-session → restart from Step 3, carry forward group/budget/style inputs
- User wants to rerun with different travel style → treat as new session with same base inputs
- Destination is domestic (same country as departure) → skip international flight costs; use train/drive estimates where relevant
- Group has mixed travel styles (e.g., half want adventure, half want relaxation) → split activity recommendations into two columns or label each activity with which subgroup it suits
