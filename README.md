# Data Engineering Part 2 - Studio

## Overview (What you’ll build)
You will create a mini ETL + Visualization pipeline in a Jupyter notebook that uses the SpaceX v4 API:

1. Extract SpaceX data, model it in 3rd Normal Form (3NF), and save CSVs to the `extraction-pipeline` folder.
2. Transform the 3NF CSVs into aggregated trend metrics and save one tidy CSV to the `transformation-pipeline`.
3. Visualize those metrics with three matplotlib charts.

Write one class per step (one class per notebook cell), then use a final cell to run the whole pipeline and render the three charts.


## Requirements & Guardrails
You must use the SpaceX v4 public API (no auth required).
- Base: https://api.spacexdata.com/v4
- Relevant endpoints (read‑only): rockets, launchpads, payloads, launches

Notebook structure:

- Cell A: Extractor class (API → 3NF CSVs)
- Cell B: Transformer class (3NF CSVs → aggregated CSV)
- Cell C: Visualizer class (aggregated CSV → 3 charts)
- Cell D: Final orchestration (instantiate classes, run end‑to‑end)

We recommend using these python libraries: 
- [requests](https://pypi.org/project/requests/), pandas, matplotlib

## Deliverables (What we expect at the end)
Extraction (3NF): CSVs in `extraction-pipeline` representing separate entities and relationships:

- Suggested tables (names are up to you, but structure matters):

    - Dimensions: dim_rocket (rocket_id, name, type, …), dim_launchpad (launchpad_id, name, region, …), dim_payload (payload_id, type, mass_kg, orbit, …)
    - Fact: fact_launch (launch_id, date_utc, rocket_id, launchpad_id, success, …)
    - Bridge (relating launches and payloads): bridge_launch_payload (launch_id, payload_id)

Transformation: A single tidy CSV in `transformation-pipeline` (e.g., aggregated_metrics.csv) containing multiple aggregations with columns like:
    - metric (str) | dimension (str) | dim_value (str/int) | value (numeric)

Visualization: 
- Three distinct matplotlib charts produced only from the aggregated CSV (no direct API calls here).

## SpaceX → 3NF Modeling (what to figure out)
Identify entities (rockets, launchpads, payloads) and a fact (launches).

Determine primary keys and foreign keys:

- SpaceX IDs are strings — keep them as your PKs/FKs.

Handle the many‑to‑many between launches and payloads with a bridge table.

Parse timestamps consistently (store ISO in extract; convert to datetime in transform).

Avoid duplicated PKs in any dimension/fact table.

Acceptance checks (Extraction):

- CSVs exist in `extraction-pipeline`.

- Each table has a clear key and no duplicate primary keys.

- Foreign keys match referenced primary keys (by dtype and values) where applicable.

## Transformations (what trends to compute)
Compute at least three meaningful metrics. Examples that fit SpaceX data:

1. Launches per year (count of launches grouped by launch year)

2. Success rate by rocket (mean of success grouped by rocket name)

3. Total payload mass per year (sum mass_kg via launch↔payload bridge, grouped by year)

4. Top‑N launchpads by launches

5. Average payload mass by orbit

Keep results in one tidy table (metric, dimension, dim_value, value) and save the aggregated data as a CSV file in `transformation-pipeline`.

Acceptance checks (Transformation):

- A single aggregated CSV exists and is tidy.

- Joins across tables are correct; types align (e.g., IDs as strings).

- Missing values are handled (e.g., mass_kg may be null → treat as 0 or drop, justify your choice).


## Visualizations (three charts)
From the aggregated CSV only, create three clear charts, e.g.:

- Line chart: Launches per Year

- Bar chart: Success Rate by Rocket (Top‑N)

- Line (or bar) chart: Total Payload Mass per Year

Chart hygiene:

- Titles, axis labels, readable ticks (rotate category labels if needed).

- No advanced styling required — clarity comes first.

Acceptance checks (Visualization):

- Exactly three charts render when the final cell runs.

- Plots are derived solely from the aggregated CSV.

## Orchestration (Final Cell)
Your last cell should:

1. Instantiate and run Extractor → writes 3NF CSVs to `extraction-pipeline`.

2. Instantiate and run Transformer → writes one aggregated CSV to `transformation-pipeline`.

3. Instantiate Visualizer, load the aggregated CSV, render the three charts.

Keep the orchestration short, readable, and reliable on a fresh kernel.

## Quality Checklist (Before You Submit)
- Uses SpaceX v4 API (rockets, launchpads, payloads, launches).

- 3NF CSVs in `extraction-pipeline` (dims, fact, bridge) with clear keys.

- Aggregated single CSV in `transformation-pipeline` with tidy schema.

- Three matplotlib charts render from the aggregated CSV.

- Classes have docstrings and minimal, helpful logging.

- Handles common failures: HTTP errors (raise_for_status, timeout), date parsing (errors='coerce'), missing numeric values.

- Notebook runs cleanly top‑to‑bottom on a fresh kernel.

## Troubleshooting Tips
HTTP issues: Check status_code; add a small retry/backoff if needed.

IDs/joins: Ensure ID columns are all strings and trimmed; confirm join cardinalities.

Dates: Convert to datetime during transform, then derive year or month.

Empty metrics: Print intermediate DataFrame heads; verify your joins and filters.

## Reminder: 

You are expected to design the 3NF schema and choose appropriate metrics using SpaceX data. Ask clarifying questions early, but try to reason it out first. Good luck!