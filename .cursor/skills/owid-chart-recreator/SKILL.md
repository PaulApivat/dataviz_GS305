# OWID Chart Recreator

## Purpose

Help recreate Our World in Data (OWID) charts (line, bar, bubble) as faithfully as possible in this repo’s Jupyter notebooks, using pandas + Matplotlib (and Plotly when requested), with correct data, filters, and OWID-style aesthetics.

## When to Use

- The user shares an OWID **grapher URL** and asks to:
  - Recreate that chart in a notebook.
  - Add a new chart to the GS305 workshop.
  - Tweak styling or data filters of an existing OWID-based chart.
- The user wants to align a chart with OWID’s **look and feel** (titles, labels, units, colors, source line).

## Relevant Repo Context

- This repo: `dataviz_GS305`
- Plans & status:
  - `.cursor/plans/owid_workshop_implementation.md`
- Notebooks (once created):
  - `notebooks/01_line_chart_clean_fuels.ipynb`
  - `notebooks/02_bar_chart_clean_fuels.ipynb`
  - `notebooks/03_line_chart_death_rate.ipynb`
  - `notebooks/04_bar_chart_death_rate.ipynb`
  - `notebooks/05_bubble_chart_clean_fuels_vs_gdp.ipynb`
  - `notebooks/06_optional_join_bubble.ipynb`

## Behavior and Guidelines

1. **Read the plan first**
   - Open `.cursor/plans/owid_workshop_implementation.md`.
   - Use:
     - The **chart list** (URLs, country filters, years).
     - The **notebook paths**.
     - The **styling guidance** (OWID-like aesthetic).
   - Align any new chart work with that structure and style.

2. **Understand the OWID chart**
   - From the OWID grapher URL:
     - Identify:
       - Indicator name and unit (e.g. `%`, `deaths per 100,000 people`).
       - Time range.
       - Country/entity filters.
       - Chart type: line, discrete bar, bubble/scatter.
     - If the chart is a bubble or multi-metric chart, determine:
       - `x`, `y`, bubble `size`, bubble `color`.
   - Note the source text and provider (e.g. WHO GHO, IHME GBD) from the “Sources & processing”/“Reuse this work” sections on OWID.

3. **Data loading strategy**
   - Prefer **CSV from OWID** via `pd.read_csv(owid_csv_url)`.
   - If the specific CSV URL is unknown:
     - Ask the user for:
       - The CSV URL (from OWID’s “Download”).
       - Or permission to assume a standard grapher CSV path.
   - When a bubble chart requires multiple datasets:
     - Either use OWID’s already-combined CSV (if available).
     - Or plan a **merge**:
       - Example: clean fuels + GDP per capita (for clean_fuels_vs_gdp chart).
       - Example: clean fuels + death rate (for indoor_pollution_vs_clean_fuels chart).

4. **Notebook structure**
   - For a new chart notebook, follow this pattern:
     1. Markdown: chart title, question, and brief explanation.
     2. Imports: `pandas`, `matplotlib.pyplot`, and `plotly.express` if interactive.
     3. Data loading cell: `pd.read_csv(owid_url)`.
     4. Data inspection cell: `head()`, `info()`, basic summaries.
     5. Data preparation:
        - Filter by countries/entities and year range.
        - Handle missing values if necessary (document if you drop/keep).
        - Rename key columns to readable names if helpful.
     6. Plotting cell(s):
        - Matplotlib line/bar or scatter/bubble.
        - Optional Plotly for interactive versions (when requested).
     7. Markdown or print cell summarizing the main takeaway (optional but encouraged for teaching).

5. **OWID-like aesthetic**
   - Use consistent style across charts:
     - Font: sans-serif (e.g. default Matplotlib sans-serif is acceptable).
     - Colors: a small, readable palette (e.g. different hues for each country).
     - Background: white; light horizontal grid on y-axis.
     - Minimal chart junk: no heavy borders, avoid overwhelming tick labels.
   - Always include:
     - **Title** matching (or closely paraphrasing) OWID’s title.
     - **Axis labels** with units.
     - A **source line** similar to OWID’s short citation, e.g.:
       - `Source: World Health Organization – Global Health Observatory (2025), processed by Our World in Data`
       - `Source: IHME, Global Burden of Disease (2025), with processing by Our World in Data`
   - If there’s a mismatch between OWID and your reproduction (e.g. slightly different colors or tick marks), aim for clarity and explain in comments if relevant pedagogically.

6. **Bubble and interactive charts**
   - For bubble charts:
     - Decide on:
       - `x` and `y` metrics (e.g. GDP per capita vs. % access to clean fuels).
       - Bubble `size` (e.g. population).
       - Bubble `color` (e.g. region or another categorization).
     - Normalize sizes so bubbles are visible but not overwhelming.
   - For Plotly versions:
     - Use `plotly.express.scatter(...)`.
     - Include hover info (country, year, and core metrics).
     - Keep styling close to the Matplotlib version when possible (titles, axis labels).

7. **Teaching and Colab compatibility**
   - Ensure:
     - No absolute local paths (only URLs or simple relative paths).
     - Cells can be run top-to-bottom with a “Run all” in Colab.
   - Favor explicit, step-by-step code over heavy abstraction; students should see clearly:
     - Where the data comes from.
     - How it’s filtered/transformed.
     - How the plot is constructed.

8. **Updating the plan and status**
   - After adding or significantly updating a chart notebook:
     - Suggest updating `.cursor/plans/owid_workshop_implementation.md`:
       - Mark the relevant notebook as in progress or done in the Status Board.
       - Add any new OWID charts to the chart list if applicable.

## Things to Avoid

- Don’t silently change the meaning of the chart (different indicator, time range, or country set) without clearly noting it.
- Don’t introduce non-reproducible steps (manual GUI exports, screenshots) into the notebook workflow.
- Don’t rely on hidden state between cells; each chart notebook should work from a fresh kernel with a simple “Run all”.