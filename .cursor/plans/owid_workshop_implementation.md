# OWID Workshop – Detailed Implementation Plan

**Purpose:** Hands-on data visualization workshop for GS305. Recreate selected Our World in Data (OWID) charts in Jupyter notebooks (Colab-friendly), match OWID aesthetic, optionally join two datasets into a bubble chart, and keep structure ready for a future Jupyter Book.

**Last updated:** From user-provided chart list and clarifying answers (March 2026).

---

## 1. Chart List and Data Sources

| # | Type | Title | OWID URL | Data / Filters |
|---|------|--------|----------|----------------|
| 1 | Line | Share of population with access to clean fuels for cooking | [grapher/access-to-clean-fuels-and-technologies-for-cooking](https://ourworldindata.org/grapher/access-to-clean-fuels-and-technologies-for-cooking?tab=line&time=earliest..2023&country=THA~IDN~MMR~LAO~KHM~VNM) | Countries: THA, IDN, MMR, LAO, KHM, VNM; time: earliest..2023 |
| 2 | Bar | Same (discrete bar, 2023) | Same grapher, `tab=discrete-bar`, `time=2023` | Same countries, year 2023 |
| 3 | Line | Death rate from indoor air pollution | [grapher/death-rate-by-source-from-indoor-air-pollution](https://ourworldindata.org/grapher/death-rate-by-source-from-indoor-air-pollution?tab=line&time=earliest..2021&country=THA~VNM~IDN~KHM~MMR~LAO) | Countries: THA, VNM, IDN, KHM, MMR, LAO; time: earliest..2021 |
| 4 | Bar | Same (discrete bar, 2021) | Same grapher, `tab=discrete-bar`, `time=2021` | Same countries, year 2021 |
| 5 | Bubble | Access to clean fuels for cooking vs. GDP per capita (2023) | [grapher/access-to-clean-fuels-for-cooking-vs-gdp-per-capita](https://ourworldindata.org/grapher/access-to-clean-fuels-for-cooking-vs-gdp-per-capita) | Year 2023; **include interactive Plotly version** |
| 6 | Bubble | Indoor air pollution death rate vs. access to clean fuels for cooking (2023) | [grapher/indoor-pollution-death-rates-clean-fuels](https://ourworldindata.org/grapher/indoor-pollution-death-rates-clean-fuels) | Year 2023; use for optional join notebook |

**Data access:** OWID grapher charts have a “Download” option (CSV). Use the CSV URL for each chart so notebooks run in Colab without local files. If a chart combines multiple datasets (e.g. bubble), the export may be one combined CSV or we merge two OWID CSV sources (clean fuels + GDP; or death rate + clean fuels) in the notebook. Document the exact CSV source in a comment in each notebook (and cite OWID + original provider per their [Reuse](https://ourworldindata.org/grapher/access-to-clean-fuels-and-technologies-for-cooking#reuse) instructions). For robustness, keep small snapshot CSVs in `notebooks/data/` as a backup if an OWID URL changes, and designate one notebook section to show students how to locate and download the OWID CSV from the grapher interface.

---

## 2. Repository Structure (Target)

```
dataviz_GS305/
├── README.md
├── requirements.txt
├── notebooks/
│   ├── 01_line_chart_clean_fuels.ipynb
│   ├── 02_bar_chart_clean_fuels.ipynb
│   ├── 03_line_chart_death_rate.ipynb
│   ├── 04_bar_chart_death_rate.ipynb
│   ├── 05_bubble_chart_clean_fuels_vs_gdp.ipynb   # + Plotly interactive
│   ├── 06_optional_join_bubble.ipynb
│   └── data/                          # optional fallback CSVs if URLs change
├── slides/
│   └── (e.g. dataviz_lecture.pdf)
└── .cursor/
    └── plans/
        └── owid_workshop_implementation.md        # this file
```

**Jupyter Book (later):** Add `docs/` with `_config.yml`, `_toc.yml`, intro page, and include the notebooks + link to `slides/dataviz_lecture.pdf`.

---

## 3. File-by-File Implementation

### 3.1 `requirements.txt`

- `pandas`
- `matplotlib`
- `plotly` (for notebook 05 only; optional dependency if you want to isolate)

No version pins so Colab’s environment works. Example:

```
pandas>=2.0
matplotlib>=3.7
plotly>=5.18
```

---

### 3.2 Shared styling (OWID-like aesthetic)

Use a small, consistent style so all charts look like OWID. Options:

- **Option A:** Add a short “style” markdown + code snippet at the top of the first notebook and tell students to copy it into others, or
- **Option B:** A single `notebooks/owid_style.py` (or `notebooks/utils_style.py`) that notebooks import; in Colab they can upload it or paste the snippet once.

**Suggested matplotlib defaults (reference for each notebook):**

- Font: sans-serif (e.g. `plt.rcParams['font.sans-serif'] = ['DejaVu Sans']` or leave default).
- Colors: discrete palette (e.g. OWID-style blues, greens, oranges) — use a list of hex codes and assign by entity/country.
- Figure: white background, light horizontal grid (`ax.yaxis.grid(True, linestyle='--', alpha=0.7)`), no heavy spines.
- Title and subtitle: chart title + “Source: …” (e.g. “Source: World Health Organization – processed by Our World in Data”).
- Legend: clear, e.g. outside or below if many series.

Implement the same logic in each notebook (or in the shared snippet) so you can tweak once.

---

### 3.3 Notebook 01: `01_line_chart_clean_fuels.ipynb`

- **Chart:** Share of population with access to clean fuels for cooking (line), countries THA, IDN, MMR, LAO, KHM, VNM, 1990–2023.
- **OWID source:** [access-to-clean-fuels-and-technologies-for-cooking](https://ourworldindata.org/grapher/access-to-clean-fuels-and-technologies-for-cooking) (line, same filters).
- **Data:** Load from OWID CSV URL (or `notebooks/data/` fallback). Filter to the six countries and year range.
- **Code flow:**
  1. Markdown: goal (recreate OWID line chart, tidy data).
  2. Imports: `pandas`, `matplotlib.pyplot`.
  3. Load data (e.g. `pd.read_csv(owid_csv_url)`).
  4. Inspect: `df.head()`, `df.info()` (tidy data discussion).
  5. Prepare: select entities and years; ensure long format (e.g. one row per country-year).
  6. Plot: one line per country, OWID-style colors and labels, title, source line.
- **Citation:** “Source: World Health Organization - Global Health Observatory (2025) – processed by Our World in Data”.

---

### 3.4 Notebook 02: `02_bar_chart_clean_fuels.ipynb`

- **Chart:** Same indicator, discrete bar, year 2023 only, same six countries.
- **Data:** Same OWID dataset; filter to `year == 2023`, same countries.
- **Code flow:** Same as 01 up to “prepare”; then bar plot (e.g. `ax.barh(countries, values)` or `ax.bar`), same style and source.
- **Citation:** Same as 01.

---

### 3.5 Notebook 03: `03_line_chart_death_rate.ipynb`

- **Chart:** Death rate from indoor air pollution (line), THA, VNM, IDN, KHM, MMR, LAO, earliest..2021.
- **OWID source:** [death-rate-by-source-from-indoor-air-pollution](https://ourworldindata.org/grapher/death-rate-by-source-from-indoor-air-pollution) (line, same filters).
- **Data:** Load from OWID CSV; filter to the six countries and time range.
- **Code flow:** Same structure as 01 (load → inspect → filter → line plot). Unit: “deaths per 100,000 people”.
- **Citation:** “Source: IHME, Global Burden of Disease (2025) – with major processing by Our World in Data”.

---

### 3.6 Notebook 04: `04_bar_chart_death_rate.ipynb`

- **Chart:** Same death rate indicator, discrete bar, year 2021, same countries.
- **Data:** Same as 03; filter to `year == 2021`.
- **Code flow:** Same as 02 but with death rate data and unit on axis.
- **Citation:** Same as 03.

---

### 3.7 Notebook 05: `05_bubble_chart_clean_fuels_vs_gdp.ipynb`

- **Chart:** Access to clean fuels for cooking vs. GDP per capita (2023), bubble chart. Plus **one interactive Plotly version** of this same chart.
- **OWID source:** [access-to-clean-fuels-for-cooking-vs-gdp-per-capita](https://ourworldindata.org/grapher/access-to-clean-fuels-for-cooking-vs-gdp-per-capita).
- **Data:** Either (a) one CSV from OWID for this chart (if available), or (b) merge clean-fuels CSV and GDP-per-capita CSV on country and year (2023). Bubble size: e.g. population (if in dataset) or constant for simplicity.
- **Code flow:**
  1. Markdown: scatter/bubble, two metrics, year 2023.
  2. Imports: `pandas`, `matplotlib`, `plotly.express` (or `plotly.graph_objects`).
  3. Load (and if needed merge) data for 2023.
  4. Inspect and prepare (x = GDP per capita, y = clean fuels %, size = population or constant).
  5. **Matplotlib:** `ax.scatter(x, y, s=...)` with OWID-like styling; label axes and add source.
  6. **Plotly:** Same x/y/size; `fig.update_layout(...)` for similar look; show in notebook.
- **Citation:** Clean fuels: WHO – OWID; GDP: use OWID’s stated source (e.g. World Bank – OWID) as on the chart page.

---

### 3.8 Notebook 06: `06_optional_join_bubble.ipynb`

- **Chart:** Indoor air pollution death rate vs. access to clean fuels for cooking (2023) — correlation bubble.
- **Pedagogic goal:** Tidy data and joins: start from two “line chart” datasets (death rate by country-year; clean fuels by country-year), join on country and year, then plot one bubble chart (e.g. x = clean fuels %, y = death rate, size = population or constant).
- **Data:** Same as 03 (death rate) and 01 (clean fuels). Filter both to 2023 (or latest common year), then `pd.merge(..., on=['country','year'])` (or equivalent entity/year columns).
- **Code flow:**
  1. Markdown: joining two datasets, long form, one row per country-year for plotting.
  2. Load dataset 1 (clean fuels), filter to 2023.
  3. Load dataset 2 (death rate), filter to 2023.
  4. Merge on country (and year if needed); handle renames so columns are clear.
  5. Inspect merged table (reinforce tidy: one row per observation).
  6. Bubble plot: x = clean fuels %, y = death rate; same OWID-style styling and citations for both sources.

---

## 4. Colab and Portability

- **No local paths:** Use only URLs for data (e.g. `pd.read_csv(owid_url)`) or paths relative to the notebook (e.g. `data/clean_fuels.csv` if you add fallback snapshot files). In Colab, if using relative paths, students can upload the repo or mount Drive and open from `notebooks/`.
- **Run order:** Each notebook should run “Run all” from top to bottom. Put data load and style setup early.
- **README:** Add a short “Workshop” section: open each notebook in Google Colab (e.g. “Open in Colab” link if repo is on GitHub), run cells in order.

---

## 5. Jupyter Book (Future)

- Add `docs/` with:
  - `_config.yml` (title, author, etc.).
  - `_toc.yml`: intro page; then one section per notebook (01–06); optional “Slides” page linking to `../slides/dataviz_lecture.pdf`.
- Intro page: brief overview and link to lecture slides (Canva export as PDF).
- Build: `jupyter-book build docs/`; output can be deployed to GitHub Pages or similar.
- Keep notebook paths and names as above so TOC stays stable.

---

## 6. What You Need to Provide (Recap)

- **Charts:** Listed above; no further chart list needed.
- **Slides:** Export from Canva as PDF (or PPT); place in `slides/` when building the book.
- **Instructor flow:** Use the same notebooks as students and explain live (no separate solution notebook).

---

## 7. Blind Spots and Mitigations

| Risk | Mitigation |
|------|------------|
| OWID CSV URL changes | Comment in each notebook the chart URL and, if known, the CSV source; keep optional `notebooks/data/` copies. |
| Colab session timeout | Instruct “Run all above” if they open mid-notebook; keep load + prepare in first cells. |
| Licensing | Every chart: “Source: [provider] – processed by Our World in Data” (or as on OWID). |
| Time overrun | Number 01–05 as core, 06 as “optional”; suggest time checkpoint after 02 or 04. |
| Plotly in Colab | Use `fig.show()` or `po.init_notebook_mode()` if needed; minimal dependencies. |
| Merge keys | In 06, document exact column names for entity and year from each OWID CSV so the join is reproducible. |

---

## 8. Implementation Order

1. Create `requirements.txt` and folders: `notebooks/`, `notebooks/data/`, `slides/`.
2. Resolve CSV URLs (or downloads) for: clean fuels, death rate, and (for 05) GDP per capita or combined bubble CSV.
3. Implement **01** and **02** (clean fuels line + bar) and lock shared style.
4. Implement **03** and **04** (death rate line + bar) reusing style.
5. Implement **05** (bubble + Plotly interactive).
6. Implement **06** (join + bubble) with clear merge steps.
7. Update README with workshop and Colab instructions.
8. (Later) Add `docs/` and Jupyter Book config when converting to book.

---

## 9. Code References (Snippets to Use)

- **Line plot (01, 03):** Filter to entities and years; loop over entities or pivot so each entity is a column; `ax.plot(df['year'], df['entity1'])` etc., or `for entity in entities: ax.plot(...)`.
- **Bar chart (02, 04):** Single-year slice; `ax.barh(df['Entity'], df['value'])` (or `ax.bar`); set color from palette by index.
- **Bubble (05, 06):** `ax.scatter(x, y, s=size_normalized, alpha=0.6, c=colors)`; add text labels for country codes/names if readable.
- **Plotly (05):** `px.scatter(df, x='gdp_per_capita', y='clean_fuels_pct', size='population', hover_name='Entity', ...)` and `fig.update_layout(font=..., title=..., ...)`.
- **Merge (06):** `pd.merge(df_clean_fuels, df_death_rate, on=['Entity','Year'], how='inner')` (adjust column names to match OWID CSV).

Use this document as the single source of truth for file paths, chart sources, and implementation order when building the notebooks.

---

## 10. Status Board

Track implementation progress. Update the Status column as you go: `⬜ Not started` | `🔄 In progress` | `✅ Done`.

### Setup and infrastructure

| Item | File / action | Status | Notes |
|------|----------------|--------|--------|
| Dependencies | `requirements.txt` | ✅ Done | pandas, matplotlib, plotly |
| Folders | `notebooks/`, `notebooks/data/`, `slides/` | ✅ Done | Created via mkdir |
| Data URLs | Resolve OWID CSV URLs for all charts | ⬜ Not started | Clean fuels, death rate, GDP (05), merge keys for 06 |
| Shared style | OWID-like matplotlib defaults | ⬜ Not started | Lock in 01/02, reuse in 03–06 |

### Notebooks

| # | Notebook | Chart(s) | Status | Notes |
|---|----------|----------|--------|--------|
| 01 | `notebooks/01_line_chart_clean_fuels.ipynb` | Line: clean fuels (THA, IDN, MMR, LAO, KHM, VNM) | ⬜ Not started | Verifiable when: data covers 1990–2023 for all 6 countries and line chart visually matches OWID trend; add assertions that years and entities match expected sets. |
| 02 | `notebooks/02_bar_chart_clean_fuels.ipynb` | Bar: clean fuels 2023 | ⬜ Not started | Verifiable when: single-year (2023) slice for all 6 countries and bar heights match OWID values within tolerance; add checks that only year 2023 is present and 6 rows exist. |
| 03 | `notebooks/03_line_chart_death_rate.ipynb` | Line: death rate indoor air pollution | ⬜ Not started | Verifiable when: time series for 1990–2021 for all 6 countries plotted with correct units; add tests that min/max years and entity set match OWID export. |
| 04 | `notebooks/04_bar_chart_death_rate.ipynb` | Bar: death rate 2021 | ⬜ Not started | Verifiable when: 2021 death rates for all 6 countries plotted; include assertion on year==2021 and row count, and spot-check one or two values. |
| 05 | `notebooks/05_bubble_chart_clean_fuels_vs_gdp.ipynb` | Bubble: clean fuels vs GDP (2023) + Plotly | ⬜ Not started | Verifiable when: merged data contains expected columns (clean_fuels_pct, gdp_per_capita, optional population) for 2023 and bubble chart appears with non-empty points; add tests on column presence and non-null counts. |
| 06 | `notebooks/06_optional_join_bubble.ipynb` | Join two datasets → bubble (death rate vs clean fuels) | ⬜ Not started | Verifiable when: join produces one row per country-year with both indicators and no unexpected duplicates; add tests on uniqueness of (entity, year), non-null indicator columns, and reasonable correlation direction. |

### Wrap-up and optional

| Item | File / action | Status | Notes |
|------|----------------|--------|--------|
| README | Workshop + Colab instructions | ⬜ Not started | |
| Slides | Export from Canva → `slides/` | ⬜ Not started | PDF or PPT |
| Jupyter Book | `docs/` with `_config.yml`, `_toc.yml`, intro, notebook chapters | ⬜ Not started | Later |
