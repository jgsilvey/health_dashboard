# Samsung Health Dashboard
 
A personal health dashboard built from real Samsung Health data, tracking daily steps and sleep against personal goals. Built entirely without writing a single line of code — using AI to handle everything from data parsing to dashboard design. I even used it to generate the first draft of this README!
 
**[View Live Dashboard →](https://jgsilvey.github.io/health_dashboard)**
 
---
 
## What It Shows
 
- **Daily steps** — color-coded against a 10,000 step goal
- **Sleep duration** — color-coded against an 8-hour goal
- **Monthly step trends** — all-time average steps by month going back to 2017
- **Goal calendar** — a visual grid showing which days hit both goals, one goal, or neither
- **Summary stats** — averages and goal hit rates that update by time period
- Filterable by last 30 days, 90 days, 6 months, 1 year, or all time
---
 
## How It Was Built
 
### Step 1 — Exporting the data
Data was exported directly from the Samsung Health app via **Settings → Download personal data**. The export produces a zip file containing CSV files for each data type. The relevant files were:
 
- `com_samsung_shealth_step_daily_trend` — daily step counts
- `com_samsung_shealth_sleep` — sleep sessions with start/end timestamps

### Step 2 — Parsing and cleaning with AI
The Samsung Health CSVs have a non-standard format — a metadata row shifts all column headers, meaning the data doesn't parse correctly out of the box. I used Claude to:
 
- Identify the column misalignment issue by inspecting raw file values
- Map the correct columns to their actual data (e.g. sleep duration had to be calculated from start/end timestamps rather than a dedicated column)
- Identify that the step data contained multiple segment records per day that summed to impossible totals (200k+ steps), and apply a MAX-per-day aggregation to get clean daily counts
- Merge the sleep and steps datasets by date

### Step 3 — Building the dashboard
Also built with Claude. The final output is a single self-contained HTML file with no external dependencies beyond Chart.js (loaded from CDN). Design decisions included:
 
- Color coding: green = both goals met, blue = steps only, purple = sleep only, red = neither
- Calendar view adjusts column count based on time range so the grid stays readable at any scale
- All data is embedded directly in the file so it works offline with no server required
---
 
## Key Findings
 
- **Steps:** Average of ~8,300 steps/day using the MAX aggregation method. The 10k goal is hit roughly 30% of days — though activity has clearly increased in recent years compared to 2020–2022.
- **Sleep:** Average of 6.97 hours/night across ~1,076 tracked nights. Only 28% of nights hit the 8-hour goal.
- **Notable pattern:** The data reveals a clear COVID-era step drop in 2020–2021, visible in the monthly trends chart.
- **Data gap:** Sleep recording is sparse before mid-2023, reflecting inconsistent watch-wearing rather than missing export data.
---
 
## What I Learned
 
This project was a good example of how AI assistance changes the nature of technical work. The hardest part wasn't building the dashboard — it was understanding the data. Samsung Health's export format is undocumented and has real quirks (shifted columns, duplicate step records, sparse sleep entries) that would have taken significant trial and error to untangle manually.
 
Using Claude to inspect raw values, hypothesize about the format, and verify fixes iteratively made that process much faster. The prompting itself required clear thinking about what the data *should* look like and why the current output didn't match — which is the same kind of reasoning that makes someone effective at working with data generally.

It's also just neat to take a look at the data this way. I'm a little unhappy with Samsung's offering in terms of dashboards, as it doesn't really provide a good "big picture" look. Despite having these goals of 10k steps and 8 hours of sleep, I've missed them a lot. But I'm improving!
 
---
 
## Updating With New Data

Unfortunately due to the way Samsung Health's data is store, automating this is a little tricky. I'll likely have to do some manual pulls to refresh the data.

To refresh the dashboard with a new Samsung Health export:
 
1. Export your data from Samsung Health → Settings → Download personal data
2. Upload the CSV files to Claude (or another AI tool)
3. Ask it to re-run the same parsing logic and regenerate the embedded data array in `index.html`
4. Replace the `RAW=[...]` array in the HTML with the new data
5. Push to GitHub — the live site updates automatically
---
 
## Tools Used
 
- **Samsung Health** — data source
- **Claude (Anthropic)** — data parsing, cleaning, dashboard design and build
- **Chart.js** — charting library
- **GitHub Pages** — hosting
