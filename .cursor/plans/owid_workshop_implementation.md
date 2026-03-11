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

**Data access:** OWID grapher charts have a “Download” option (CSV) and table views. For this workshop, we treat those as the authoritative source, but to avoid runtime/API issues we work from **small CSV snapshots** stored under `notebooks/data/`. For each chart, we manually download or copy the relevant OWID table, filter to the required entities/years, and save a compact CSV (e.g. `clean_fuels.csv`, `indoor_pollution.csv`). All notebooks load from these snapshot CSVs (not live OWID URLs) and document the original OWID chart URLs and citations.

---

## 2. Repository Structure (Target)

```
dataviz_GS305/
├── README.md
├── requirements.txt
├── notebooks/
│   ├── 01_line_chart_clean_fuels.ipynb              # line + bar
│   ├── 03_line_chart_death_rate.ipynb               # line + bar
│   ├── 05_bubble_chart_clean_fuels_vs_gdp.ipynb     # + Plotly interactive
│   ├── 06_optional_join_bubble.ipynb
│   └── data/                                        # CSV snapshots for all charts
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

### 3.3 Chart specification checklist (per chart / notebook)

For more complex charts—especially interactive Plotly charts—start by writing a short **chart specification checklist** in a markdown cell inside the notebook before you fine-tune visuals. The checklist is based on the OWID chart and acts as a contract the code should satisfy.

A typical checklist includes:

- **Data subset and filters:** Which year(s), which countries, and how missing data are handled.
- **Axes and ranges:** Linear vs log scale, expected tick values and labels, and y-axis range (e.g. 20–100% for access to clean fuels).
- **Encodings:** Which column is mapped to x, y, color, bubble size, and text labels (e.g. color by `World region according to OWID`, bubble size from population, labels for countries above a population threshold).
- **Interactivity:** Hover fields (e.g. Entity, Year, GDP per capita, indicator value, Population), legend behavior, and default zoom.
- **Annotations / notes:** Any always-visible labels, explanatory notes (e.g. “Circles sized by population”), or OWID-specific design details.

Workflow for new charts (especially 05 and 06):

1. Inspect the OWID chart and write a markdown checklist that describes its behavior.
2. Implement the Matplotlib / Plotly chart code to satisfy that checklist.
3. When refining the chart, update the checklist first, then adjust the code.

Notebook `05_bubble_chart_clean_fuels_vs_gdp.ipynb` already contains such a checklist for the interactive Plotly chart; use it as a template for future charts that need closer OWID matching.

---

### 3.3 Notebook 01: `01_line_chart_clean_fuels.ipynb`

- **Chart:** Share of population with access to clean fuels for cooking (line and bar), countries THA, IDN, MMR, LAO, KHM, VNM, using a workshop snapshot for **2000** and **2023**.
- **OWID source:** [access-to-clean-fuels-and-technologies-for-cooking](https://ourworldindata.org/grapher/access-to-clean-fuels-and-technologies-for-cooking) (line, same filters).
- **Data:** Snapshot CSV `notebooks/data/clean_fuels.csv` with one row per country (Thailand, Vietnam, Indonesia, Cambodia, Myanmar, Laos) and columns including 2000 and 2023 values and change metrics.
- **Code flow:**
  1. Markdown: goal (recreate an OWID-style chart from a CSV snapshot; discuss tidy data).
  2. Imports: `pandas`, `matplotlib.pyplot`, and apply OWID-inspired Matplotlib style (figure size, grid, light spines).
  3. Load snapshot: `pd.read_csv("data/clean_fuels.csv")`.
  4. Inspect: `df.head()`, `df.info()` to see wide format (country as rows, year columns).
  5. Reshape to tidy long format:
     - Melt year columns (e.g. 2000 and 2023) into `Year` and `clean_fuels_pct`.
     - Strip `%` and convert to numeric.
     - Rename `Country or region` → `Entity`.
     - Map `Entity` to ISO `Code` (THA, VNM, IDN, KHM, MMR, LAO).
  6. Filter / assert:
     - Keep only the six codes and years {2000, 2023}.
     - Assertions on `Code` set and min/max `Year` (== 2000 / 2023).
  7. Plot line chart:
     - One line per country using `clean_fuels_pct` vs `Year`.
     - Label each line at its endpoint with the country name (no legend).
     - OWID-style axes/title and a source line below the figure.
  8. Plot bar chart (2023):
     - Horizontal bar chart of 2023 `clean_fuels_pct` by country.
     - Percentage labels (e.g. “86.8%”) at the end of each bar.
     - Same source line and styling.
- **Citation:** “Source: World Health Organization - Global Health Observatory (2025) – processed by Our World in Data”.

---

### 3.4 Notebook 03: `03_line_chart_death_rate.ipynb`

- **Chart:** Death rate from indoor air pollution (line), THA, VNM, IDN, KHM, MMR, LAO, earliest..2021.
- **OWID source:** [death-rate-by-source-from-indoor-air-pollution](https://ourworldindata.org/grapher/death-rate-by-source-from-indoor-air-pollution) (line, same filters).
- **Data:** Snapshot CSV `notebooks/data/indoor_pollution.csv` with one row per country–year (THA, VNM, IDN, KHM, MMR, LAO) and columns including year and death rate from indoor air pollution.
- **Code flow:** Load snapshot, ensure tidy columns (`Entity`, `Code`, `Year`, `death_rate`), filter to target years/countries, plot one line per country (optionally label endpoints instead of using a legend).
- **Citation:** “Source: IHME, Global Burden of Disease (2025) – with major processing by Our World in Data”.

---

### 3.5 Notebook 05: `05_bubble_chart_clean_fuels_vs_gdp.ipynb`

- **Chart:** Access to clean fuels for cooking vs. GDP per capita (2023), bubble chart. Plus **one interactive Plotly version** of this same chart.
- **OWID source:** [access-to-clean-fuels-for-cooking-vs-gdp-per-capita](https://ourworldindata.org/grapher/access-to-clean-fuels-for-cooking-vs-gdp-per-capita).
- **Data:** Snapshot CSV `notebooks/data/clean_fuels_vs_gdp_per_capita.csv` with one row per country (or per country–year) including clean fuels (%) and GDP per capita for 2023, plus optional population.
- **Code flow:** Load snapshot, ensure tidy columns (`Entity`, `Code`, `Year`, `clean_fuels_pct`, `gdp_per_capita`, optional `population`), filter to 2023, then:
  - Matplotlib bubble: `clean_fuels_pct` vs `gdp_per_capita`, bubble size = population or constant, labels and source.
  - Plotly version: same data with hover tooltips and interactive legend.
- **Citation:** Clean fuels: WHO – OWID; GDP: use OWID’s stated source (e.g. World Bank – OWID) as on the chart page.

---

### 3.6 Notebook 06: `06_optional_join_bubble.ipynb`

- **Chart:** Indoor air pollution death rate vs. access to clean fuels for cooking (2023) — correlation bubble.
- **Pedagogic goal:** Tidy data and joins: Have students start from two “line chart” datasets (death rate by country-year; clean fuels by country-year), join on country and year, then plot one bubble chart (e.g. x = clean fuels %, y = death rate, size = population or constant). As strictly a **backup benchmark**, use `notebooks/data/indoor_pollution_vs_clean_fuels_2023.csv` for the data to show what students output should look like. 
- **Data:** Snapshot CSVs `notebooks/data/indoor_pollution.csv` and `notebooks/data/clean_fuels.csv` (or two long tables for death rate and clean fuels). Join on country code and year to get a tidy table with both indicators for 2021 as the last year available for both datasets. Then in a separate cell, use `notebooks/data/indoor_pollution_vs_clean_fuels_2023.csv` as a comparison bubble chart. 
- **Code flow:** Load snapshots, keep tidy long format, merge on `Code` + `Year`, verify uniqueness and non-null indicators, then plot bubble (x = clean fuels %, y = death rate) with OWID-like styling and combined citation. Compare it to the backup benchmark. 

---

## 4. Colab and Portability

- **No local absolute paths:** Use paths relative to the notebook (e.g. `data/clean_fuels.csv`, `data/indoor_pollution.csv`) for the snapshot CSVs stored under `notebooks/data/`. In Colab, students can open the notebook from Drive or GitHub and keep the `data/` folder alongside it.
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
3. Implement **01** (clean fuels line + bar) and lock shared style.
4. Implement **03** (death rate line + bar) reusing style.
5. Implement **05** (bubble + Plotly interactive), starting by writing a chart specification checklist in the notebook based on the OWID chart and then building Matplotlib and Plotly charts to satisfy that checklist.
6. Implement **06** (join + bubble) with clear merge steps.
7. Update README with workshop and Colab instructions.
8. (Later) Add `docs/` and Jupyter Book config when converting to book.

---

## 9. Code References (Snippets to Use)

- **Line plot (01, 03):** Filter/reshape snapshot data to tidy long format (`Entity`, `Code`, `Year`, `value`), group by code, and plot with line labels at endpoints instead of a legend.
- **Bar chart (01, 03):** Single-year slice from tidy table; `ax.barh(df['Entity'], df['value'])` (or `ax.bar`); add percentage/value labels at the end of each bar and keep OWID-style axes/title.
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
| Data snapshots | Prepare OWID-based CSV snapshots in `notebooks/data/` for all charts | ✅ Done | Clean fuels, GDP vs clean fuels, indoor pollution, and 2023 indoor pollution vs clean fuels snapshots created; verify join-ready format while implementing 06 |
| Shared style | OWID-like matplotlib defaults | 🔄 In progress | Basic style applied in 01; reuse and refine in 02–06 |

### Notebooks

| # | Notebook | Chart(s) | Status | Notes |
|---|----------|----------|--------|--------|

| 01 | `notebooks/01_line_chart_clean_fuels.ipynb` | Line + bar: clean fuels snapshot (THA, IDN, MMR, LAO, KHM, VNM) | ✅ Done | Verifiable when: snapshot CSV is reshaped to tidy long format; Codes match the 6 expected countries; years exactly {2000, 2023}; line chart labels endpoints instead of legend; 2023 bar chart shows the same values with percentage labels. |
| 03 | `notebooks/03_line_chart_death_rate.ipynb` | Line + bar: indoor air pollution death rate snapshot | ✅ Done | Verifiable when: tidy long table loaded from snapshot, Codes match the 6 expected countries, years exactly {1990, 2021}; line chart labels endpoints; 2021 bar chart shows the same values with unit on axis and optional value labels. |
| 05 | `notebooks/05_bubble_chart_clean_fuels_vs_gdp.ipynb` | Bubble: clean fuels vs GDP (2023) + Plotly | ✅ Done | Verifiable when: 2023 data is filtered with non-null `clean_fuels_pct`, `gdp_per_capita`, and `Population`; Matplotlib bubble chart matches OWID-style axes/title and shows visible bubbles; Plotly chart uses log x-axis with specified ticks, colors by `World region according to OWID`, scales bubble size from population with clearly visible large countries (e.g. China, India), shows always-visible labels for larger countries, a custom hovertemplate (Entity, Year, GDP per capita, clean fuels %, Population), and the notebook includes a markdown checklist describing the intended OWID spec. |
| 06 | `notebooks/06_optional_join_bubble.ipynb` | Join two datasets → bubble (death rate vs clean fuels) | ⬜ Not started | Verifiable when: join of snapshot tables produces one row per country-year with both indicators and no unexpected duplicates; add tests on uniqueness of (entity, year), non-null indicator columns, and reasonable correlation direction. |

### Wrap-up and optional

| Item | File / action | Status | Notes |
|------|----------------|--------|--------|
| README | Workshop + Colab instructions | ⬜ Not started | |
| Slides | Export from Canva → `slides/` | ⬜ Not started | PDF or PPT |
| Jupyter Book | `docs/` with `_config.yml`, `_toc.yml`, intro, notebook chapters | ⬜ Not started | Later |
