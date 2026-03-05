# OWID Tidy Join Coach

## Purpose

Guide the process of joining and reshaping Our World in Data (OWID) datasets for this repo, with a focus on tidy data principles. Help combine multiple OWID indicators (e.g. two line-chart datasets) into a single tidy table suitable for charts like bubble plots, while making the join logic and data structure clear for students.

## When to Use

- You need to:
  - Combine two or more OWID indicators (e.g. clean fuels + GDP per capita, or death rate + clean fuels).
  - Turn two line charts into a single bubble/scatter chart.
  - Demonstrate **long vs wide** formats and joins in a teaching context.
- You’re working on:
  - `notebooks/06_optional_join_bubble.ipynb`, or
  - Any other notebook that merges OWID datasets by country/entity and year.

## Relevant Repo Context

- Plan and status:
  - `.cursor/plans/owid_workshop_implementation.md`
    - Especially sections on:
      - Notebook 06: optional join bubble.
      - Tidy data and joins.
- Notebooks (targets):
  - `notebooks/06_optional_join_bubble.ipynb`
  - Potentially `notebooks/05_bubble_chart_clean_fuels_vs_gdp.ipynb` if it uses a join.
- Data sources:
  - OWID grapher CSVs for:
    - Access to clean fuels for cooking.
    - Death rate from indoor air pollution.
    - GDP per capita (if applicable).

## Behavior and Guidelines

1. **Start from the plan**
   - Read `.cursor/plans/owid_workshop_implementation.md`:
     - Confirm which indicators and years are being joined.
     - Note the planned role of notebook 06 (two line datasets → one bubble chart).
   - Ensure your join and reshaping work fits the described flow and chart.

2. **Clarify join intent**
   - For each join, explicitly identify:
     - The **entities** (e.g. `Entity`, `Country`, `location`) being matched.
     - The **time key** (usually `Year`).
     - The **indicators** being brought together (e.g. `% access to clean fuels`, `death rate from indoor air pollution`, `GDP per capita`).
   - Decide:
     - Inner vs left join:
       - Default to **inner** unless there’s a clear pedagogical reason to keep unmatched rows.
     - How to handle missing data:
       - Drop with explanation, or
       - Impute/simple fill with explanation (generally less desirable for teaching unless it’s the topic).

3. **Emphasize tidy data principles**
   - Aim for:
     - One row per observation (e.g. one row per country–year).
     - Columns for:
       - Identifiers: `entity`, `year` (and possibly region).
       - Variables: `clean_fuels_pct`, `indoor_air_pollution_death_rate`, `gdp_per_capita`, etc.
   - If starting from a wide table:
     - Demonstrate reshaping with `melt`, `pivot`, or `wide_to_long` as needed.
   - Use clear column names:
     - Avoid cryptic codes if possible; rename to descriptive, snake_case names that match the lecture vocabulary.

4. **Step-by-step join workflow**
   - Encourage a structured sequence in notebooks:
     1. **Load datasets** separately, one per indicator.
     2. **Inspect**:
        - Column names, sample rows (`head()`).
        - Unique entities and years.
     3. **Align keys**:
        - Ensure join columns have the same names and types (e.g. both `Entity` and `Year`).
        - If needed, rename: `df_clean.rename(columns={"Entity": "country"}, inplace=True)` or similar.
     4. **Perform the join**:
        - Example:
          - `df_merged = pd.merge(df_clean, df_death, on=["Entity", "Year"], how="inner")`
        - Verify shape and row count against expectations.
     5. **Post-join checks**:
        - Confirm no unexpected duplication (e.g. multiple rows per country-year) unless that’s intended and explained.
        - Quickly inspect aggregated stats (e.g. min/max, null counts) for each indicator.
     6. **Prepare for visualization**:
        - Select only needed columns for plotting.
        - Optionally filter to a single year (e.g. 2023) or a subset of countries.

5. **Teaching-oriented commentary**
   - Encourage explanatory comments or markdown where helpful:
     - Why these join keys are chosen.
     - What can go wrong (e.g. mismatched country naming, missing years).
     - How long vs wide affects plotting libraries.
   - Keep code readable:
     - Use intermediate variables for steps: `df_clean_2023`, `df_death_2023`, `df_merged_2023`.

6. **Integration with visualization**
   - Work closely with the chart goals:
     - For notebook 06:
       - Prepare columns like:
         - `clean_fuels_pct` → x-axis
         - `indoor_air_pollution_death_rate` → y-axis
         - `population` (if available) → bubble size
       - Ensure the merged table is ready for a direct `scatter`/bubble call.
   - Maintain OWID-aligned **sources and units**:
     - Ensure units are preserved and clearly indicated in axis labels (e.g. `%`, `deaths per 100,000`).
   - If merging indicators with very different scales, consider:
     - Explaining log vs linear scale choices.
     - Checking for outliers that may skew the chart.

7. **Colab and reproducibility**
   - Use:
     - OWID CSV URLs as primary sources.
     - Or relative paths to small CSVs in `notebooks/data/` as fallback (if URLs change).
   - No local absolute paths.
   - Ensure a fresh Colab kernel can:
     - Run all cells in order.
     - Reach the joined tidy table and chart without manual intervention.

8. **Plan and Status Board updates**
   - After implementing or refining a join (especially in notebook 06):
     - Propose updates to the Status Board in `.cursor/plans/owid_workshop_implementation.md`:
       - Mark 06 as `In progress` or `Done`.
       - Add notes about:
         - Which OWID indicators are joined.
         - Any important caveats (e.g. certain countries dropped due to missing data).

## Things to Avoid

- Don’t silently change join keys or filter criteria in ways that alter the story without documenting the change.
- Don’t hide complex reshaping steps in a single opaque line of code without explanation in teaching notebooks.
- Don’t rely on “magic” index-based joins (e.g. joining by position) when semantic keys (country, year) are available.

## Interaction Pattern

When this skill is active and the user asks for help with joins or turning two OWID charts into one bubble/correlation chart:

1. Inspect the relevant notebook (often `notebooks/06_optional_join_bubble.ipynb`) and the plan.
2. Identify the indicators, join keys, and desired output chart.
3. Propose and (when allowed) implement a clear, stepwise join and reshape pipeline that follows tidy data principles.
4. Verify the merged table and resulting chart behave as expected.
5. Suggest updates to the plan’s Status Board and, if needed, brief textual explanations to reinforce the tidy data concepts for students.
