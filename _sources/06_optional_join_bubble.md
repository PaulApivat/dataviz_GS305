# Optional join bubble chart: Indoor air pollution vs access to clean fuels

In this optional notebook we recreate an Our World in Data (OWID)-style **bubble chart** showing the relationship between **indoor air pollution death rate** and **access to clean fuels for cooking**.

Teaching goals:
- Start from **two separate wide snapshots** (clean fuels and indoor air pollution).
- Reshape each into **tidy long format**.
- **Join** them on country and year.
- Build an interactive **Plotly** bubble chart from the joined table.
- Compare the result to a **pre-joined benchmark** CSV for the same year (2021).

We mirror the style and workflow used in `05_bubble_chart_clean_fuels_vs_gdp.ipynb`, including a small chart specification checklist before we fine-tune the interactive chart.



```python
import pandas as pd
import matplotlib.pyplot as plt
import plotly.express as px

# Matplotlib style settings (OWID-inspired)
plt.style.use("default")
plt.rcParams["figure.figsize"] = (10, 6)
plt.rcParams["axes.grid"] = True
plt.rcParams["grid.alpha"] = 0.3
plt.rcParams["axes.spines.top"] = False
plt.rcParams["axes.spines.right"] = False

```


```python
# Load wide snapshots for clean fuels and indoor pollution (2000 & 2021)

clean_path = "data/clean_fuels_2000_2021.csv"
poll_path = "data/indoor_pollution_2000_2021.csv"

df_clean_wide = pd.read_csv(clean_path)
df_poll_wide = pd.read_csv(poll_path)

df_clean_wide.head(), df_poll_wide.head()

```




    (  Country or region    2000    2021  1990–2023  Absolute Change (pp)  \
     0          Cambodia   3.40%  46.30%        NaN                  42.9   
     1         Indonesia   6.70%  86.90%        NaN                  80.2   
     2              Laos   1.30%   9.60%        NaN                   8.3   
     3           Myanmar   1.70%  44.50%        NaN                  42.8   
     4          Thailand  59.90%  85.20%        NaN                  25.3   
     
       Relative Change  
     0           1262%  
     1           1197%  
     2            638%  
     3           2518%  
     4             42%  ,
       Country or region   2000   2021  1990–2023  Absolute Change Relative Change
     0          Cambodia  206.4  127.5        NaN            -78.9            -38%
     1         Indonesia  102.4   30.0        NaN            -72.3            -71%
     2              Laos  295.1  202.5        NaN            -92.6            -31%
     3           Myanmar  317.7  207.9        NaN           -109.9            -35%
     4          Thailand   53.2    4.6        NaN            -48.6            -91%)




```python
# Reshape to tidy long format for 2000 and 2021

# Clean fuels snapshot: wide → long
clean_year_cols = ["2000", "2021"]
df_clean_long = df_clean_wide.melt(
    id_vars=["Country or region"],
    value_vars=clean_year_cols,
    var_name="Year",
    value_name="clean_fuels_pct_raw",
)

df_clean_long["Year"] = df_clean_long["Year"].astype(int)
df_clean_long["clean_fuels_pct"] = (
    df_clean_long["clean_fuels_pct_raw"].str.rstrip("%") .astype(float)
)
df_clean_long = df_clean_long.rename(columns={"Country or region": "Entity"})

# Indoor pollution snapshot: wide → long
poll_year_cols = ["2000", "2021"]
df_poll_long = df_poll_wide.melt(
    id_vars=["Country or region"],
    value_vars=poll_year_cols,
    var_name="Year",
    value_name="death_rate",
)

df_poll_long["Year"] = df_poll_long["Year"].astype(int)
df_poll_long = df_poll_long.rename(columns={"Country or region": "Entity"})

# Map Entity to ISO Code for the six countries in the workshop
country_to_code = {
    "Thailand": "THA",
    "Vietnam": "VNM",
    "Indonesia": "IDN",
    "Cambodia": "KHM",
    "Myanmar": "MMR",
    "Laos": "LAO",
}

df_clean_long["Code"] = df_clean_long["Entity"].map(country_to_code)
df_poll_long["Code"] = df_poll_long["Entity"].map(country_to_code)

# Filter to rows where codes are known and years are in {2000, 2021}
df_clean_long = df_clean_long[df_clean_long["Code"].notna() & df_clean_long["Year"].isin([2000, 2021])]
df_poll_long = df_poll_long[df_poll_long["Code"].notna() & df_poll_long["Year"].isin([2000, 2021])]

# Simple checks
assert set(df_clean_long["Code"]) == set(country_to_code.values())
assert set(df_poll_long["Code"]) == set(country_to_code.values())
assert set(df_clean_long["Year"]) == {2000, 2021}
assert set(df_poll_long["Year"]) == {2000, 2021}

df_clean_long.head(), df_poll_long.head()

```




    (      Entity  Year clean_fuels_pct_raw  clean_fuels_pct Code
     0   Cambodia  2000               3.40%              3.4  KHM
     1  Indonesia  2000               6.70%              6.7  IDN
     2       Laos  2000               1.30%              1.3  LAO
     3    Myanmar  2000               1.70%              1.7  MMR
     4   Thailand  2000              59.90%             59.9  THA,
           Entity  Year  death_rate Code
     0   Cambodia  2000       206.4  KHM
     1  Indonesia  2000       102.4  IDN
     2       Laos  2000       295.1  LAO
     3    Myanmar  2000       317.7  MMR
     4   Thailand  2000        53.2  THA)




```python
# Join clean fuels and indoor pollution on Code + Year

cols_clean = ["Entity", "Code", "Year", "clean_fuels_pct"]
cols_poll = ["Entity", "Code", "Year", "death_rate"]

df_clean_small = df_clean_long[cols_clean].copy()
df_poll_small = df_poll_long[cols_poll].copy()

df_joined = pd.merge(
    df_clean_small,
    df_poll_small,
    on=["Code", "Year"],
    suffixes=("_clean", "_poll"),
)

# Use the clean-fuels Entity label as the main Entity
df_joined["Entity"] = df_joined["Entity_clean"]
df_joined = df_joined[["Entity", "Code", "Year", "clean_fuels_pct", "death_rate"]]

# Check for duplicates and nulls
assert df_joined.set_index(["Code", "Year"]).index.is_unique
assert df_joined[["clean_fuels_pct", "death_rate"]].notna().all().all()

df_joined.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Entity</th>
      <th>Code</th>
      <th>Year</th>
      <th>clean_fuels_pct</th>
      <th>death_rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Cambodia</td>
      <td>KHM</td>
      <td>2000</td>
      <td>3.4</td>
      <td>206.4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Indonesia</td>
      <td>IDN</td>
      <td>2000</td>
      <td>6.7</td>
      <td>102.4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Laos</td>
      <td>LAO</td>
      <td>2000</td>
      <td>1.3</td>
      <td>295.1</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Myanmar</td>
      <td>MMR</td>
      <td>2000</td>
      <td>1.7</td>
      <td>317.7</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Thailand</td>
      <td>THA</td>
      <td>2000</td>
      <td>59.9</td>
      <td>53.2</td>
    </tr>
  </tbody>
</table>
</div>



## Chart specification checklist (delta from notebook 05)

We reuse the checklist structure from `05_bubble_chart_clean_fuels_vs_gdp.ipynb`, but adapt it to this new chart:

- **Data subset and filters**
  - Use the **joined table** `df_joined` for the six countries (THA, VNM, IDN, KHM, MMR, LAO).
  - Focus on **Year = 2021** for the main bubble chart.
- **Axes and ranges**
  - x-axis: `clean_fuels_pct` (share of population with access to clean fuels for cooking, in %), linear scale, range ~0–100, ticks at 20, 40, 60, 80, 100.
  - y-axis: `death_rate` (indoor air pollution death rate, deaths per 100,000 people), linear scale; range chosen so all six countries are clearly visible.
- **Encodings**
  - x: `clean_fuels_pct`
  - y: `death_rate`
  - size: constant (same bubble size for each country, to keep focus on position rather than population).
  - text labels: always-visible **country names** (`Entity`) for each point, placed above the bubble.
- **Interactivity (Plotly)**
  - Hover fields: Entity, Year, clean fuels %, death rate.
  - Legend: not strictly needed (only six countries, labeled directly).
- **Annotations / notes**
  - Optional note about interpretation: higher clean fuels % should generally correspond to **lower** indoor air pollution death rates.



```python
# Plotly bubble chart: joined data (2021)

df_2021 = df_joined[df_joined["Year"] == 2021].copy()

fig_join = px.scatter(
    df_2021,
    x="clean_fuels_pct",
    y="death_rate",
    hover_name="Entity",
    text="Entity",
    title="Indoor air pollution death rate vs access to clean fuels (2021)",
)

fig_join.update_layout(
    template="plotly_white",
    xaxis_title="Access to clean fuels and technologies for cooking (%)",
    yaxis_title="Indoor air pollution death rate (deaths per 100,000 people)",
)

fig_join.update_xaxes(showgrid=True, gridcolor="rgba(0,0,0,0.1)", range=[0, 100])
fig_join.update_yaxes(showgrid=True, gridcolor="rgba(0,0,0,0.1)")

fig_join.update_traces(
    marker=dict(size=16, opacity=0.8),
    textposition="top center",
    textfont=dict(size=10),
    hovertemplate="<b>%{hovertext}</b><br>Year: %{customdata[0]}<br>Clean fuels: %{x:.1f}%<br>Death rate: %{y:.1f}<extra></extra>" if False else None,
)

fig_join.show()

```




```python
# Benchmark comparison: pre-joined 2021 CSV

benchmark_path = "data/indoor_pollution_vs_clean_fuels_2021.csv"

# Read without assuming header; find the row where the first column is "Country or region"
df_raw = pd.read_csv(benchmark_path, header=None)
header_row_idx = df_raw.index[df_raw.iloc[:, 0] == "Country or region"][0]

# Use that row as header, and everything below as data
df_benchmark = df_raw.iloc[header_row_idx + 1 :].copy()
df_benchmark.columns = [
    "Country or region",           # name
    "death_rate",                  # indoor air pollution death rate (2021)
    "clean_fuels_pct_raw",         # clean fuels (%), as string with %
    "World region according to OWID",
]

df_benchmark.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Country or region</th>
      <th>death_rate</th>
      <th>clean_fuels_pct_raw</th>
      <th>World region according to OWID</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>Afghanistan</td>
      <td>241.7</td>
      <td>35.90%</td>
      <td>Asia</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Africa (WHO)</td>
      <td>NaN</td>
      <td>22.53%</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>African Region (WHO)</td>
      <td>101.9</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Aland Islands</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Europe</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Albania</td>
      <td>20.3</td>
      <td>84.50%</td>
      <td>Europe</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Simple Plotly chart for the benchmark data (all countries with valid values)
# Start from the full benchmark (already cleaned in the previous cell)
df_bench_small = df_benchmark.copy()
# Rename and convert indicator columns
df_bench_small = df_bench_small.rename(columns={"Country or region": "Entity"})

df_bench_small["death_rate"] = pd.to_numeric(df_bench_small["death_rate"], errors="coerce")
df_bench_small["clean_fuels_pct"] = (
    df_bench_small["clean_fuels_pct_raw"]
    .astype(str)
    .str.replace("%", "", regex=False)
    .str.strip()
    .astype(float)
)

# Drop rows where either indicator is missing
df_bench_small = df_bench_small.dropna(subset=["death_rate", "clean_fuels_pct"])

# Label only a subset of countries to reduce clutter (per OWID spec)
label_countries = [
    "Solomon Islands",
    "Haiti",
    "Afghanistan",
    "Central African Republic",
    "Bangladesh",
    "Myanmar",
    "Democratic Republic of Congo",
    "Burundi",
    "Micronesia (country)",
    "Yemen",
    "Benin",
    "Cameroon",
    "Nepal",
    "Cambodia",
    "Marshall Islands",
    "Burkina Faso",
    "Congo",
    "Mauritania",
    "India",
    "Tajikistan",
    "Comoros",
    "Angola",
    "Kyrgyzstan",
    "Tuvalu",
    "Djibouti",
    "Bosnia and Herz.",
    "Botswana",
    "Albania",
    "Nauru",
    "Algeria",
]
df_bench_small["label"] = df_bench_small["Entity"].where(
    df_bench_small["Entity"].isin(label_countries)
)

# OWID-like color palette by world region
color_map = {
    "North America": "rgb(229, 110, 90)",
    "South America": "rgb(136, 48, 57)",
    "Africa": "rgb(162, 85, 156)",
    "Europe": "rgb(76, 106, 156)",
    "Asia": "rgb(9, 132, 126)",
    "Oceania": "rgb(56, 170, 186)",
}

fig_bench = px.scatter(
    df_bench_small,
    x="clean_fuels_pct",
    y="death_rate",
    hover_name="Entity",
    text="label",
    color="World region according to OWID",
    color_discrete_map=color_map,
    title="Indoor air pollution death rate vs access to clean fuels for cooking, 2021",
)

fig_bench.update_layout(
    template="plotly_white",
    xaxis_title="Share of population with access to clean fuels for cooking (%)",
    yaxis_title="Indoor air pollution death rate (deaths per 100,000 people)",
    legend=dict(orientation="v", x=1.02, y=1),
)
fig_bench.update_xaxes(
    showgrid=True,
    gridcolor="rgba(0,0,0,0.1)",
    range=[0, 100],
    tickvals=[0, 20, 40, 60, 80, 100],
)
fig_bench.update_yaxes(
    showgrid=True,
    gridcolor="rgba(0,0,0,0.1)",
    range=[0, 400],
    tickvals=[0, 50, 100, 150, 200, 250, 300, 350, 400],
)
fig_bench.update_traces(
    marker=dict(size=10, opacity=0.8),
    textposition="top center",
    textfont=dict(size=9),
    hovertemplate=(
        "<b>%{hovertext}</b><br>"
        "Year: 2021<br>"
        "Clean fuels: %{x:.1f}%<br>"
        "Indoor air pollution: %{y:.1f} deaths per 100,000<extra></extra>"
    ),
)

fig_bench.show()

```



Checklist:
1. OWID chart spec:
    - data subset: Year = 2021 (We're comparing 'Indoor air pollution death rate vs. access to clean fuels for cooking, 2021')
    - all countries are visible
    - missing data are excluded
    - X-axis title and unit: Share of population with acess to clean fuels for cooking (%)
    - Y-axis title and unit: Indoor air pollution death rate (deaths per 100,000 people)
    - X-axis range: 0%, 20%, 40%, 60%, 80%, 100%
    - Y-axis range: 0, 50, 100, 150, 200, 250, 300, 350, 400
    - Color is mapped to continent (North America, South America, Africa, Europe, Asia, Oceania)
    - All dots are the same size
    - OWID shows a legend by continents
    - Labels only for a subset of countries to reduce clutter (Solomon Islands, Haiti, Afghanistan, Central Africa Republic, Bangladesh, Myanmar, DR Congo, Burundi, Micronesia (country), Yemen, Benin, Cameroon, Nepal, Cambodia, Marshall Islands, Burkina Faso, Congo, Mauritania, India, Tajikistan, Comoros, Angola, Kyrgyzstan, Tuvalu, Djibouti, Bosnia and Herz., Botswana, Albania, Nauru, Algeria)
    - Hover fields: Name, Year, Share of population with access to clean fuels and technologies for cooking (%), Indoor air pollution death rate (death per 100,000 people)
    - Default zoom extent: Yes
    - Reference lines or annotations (omit subtitle to reduce clutter).
2. Lock data parity:
    - filter to 2021 (done)
    - use all countries OWID does by default
    - columns confirmed
3. Match scales and axes:
    - X-axis range: 0%, 20%, 40%, 60%, 80%, 100%
    - Y-axis range: 0, 50, 100, 150, 200, 250, 300, 350, 400
    - OWID Title: "Indoor air pollution death rate vs. access to clean fuels for cooking, 2021"
4. Match color and size encoding:
    - OWID colors by continent: color="World continents according to OWID"
    - Custom color sequence: (North America, rgb(229, 110, 90); South America, rgb(136, 48, 57); Africa, rgb(162, 85, 156); Europe, rgb(76, 106, 156); Asia, rgb(9, 132, 126); Oceania, rgb(56, 170, 186))
5. Fine-tune layout (background, grid, legend)
    - template / background: fig.update_layout(template="plotly_white")
    - gridlines & spines: 
        - fig.update_xaxes(showgrid=True, gridcolor="rgba(0,0,0,0.1)")
        - fig.update_yaxes(showgrid=True, gridcolor="rgba(0,0,0,0.1)")
    - legend: fig.update_layout(legend=dict(orientation="v", x=1.02, y=1))
    - legend colored by continents
6. Customize hover tooltips
    - OWID hover cards: Name, Year, Share of population with access to clean fuels and technologies for cooking (%), Indoor air pollution death rate (death per 100,000 people)
    ```
        fig.update_traces(
        hovertemplate=(
            "<b>%{hovertext}</b><br>"  # Entity
            "Clean fuels: %{y:.1f}%<br>"
            "Indoor air pollution: %{x:,.0f}<br>"
        )
    )
    ```
7. Final polish: labels and annotations
    - note: none

## Licensing, attribution, and how to obtain the data

- Data and original charts: Our World in Data, *Indoor air pollution death rate* and *Access to clean fuels for cooking*.
- Indoor air pollution indicator source: IHME, Global Burden of Disease (GBD).
- Clean fuels indicator source: World Health Organization – Global Health Observatory (GHO).
- OWID and its code/visualizations are released under [Creative Commons BY](https://ourworldindata.org/grapher/death-rate-by-source-from-indoor-air-pollution#reuse); original providers retain their own licenses.

To obtain the CSVs and use them in this notebook:
1. Open the relevant OWID chart pages for indoor air pollution and access to clean fuels.
2. Use the **"Download"** or **"Data"** tab to export the tables as CSV, or copy the tables.
3. Create the wide snapshots `clean_fuels_2000_2021.csv` and `indoor_pollution_2000_2021.csv` (with 2000 and 2021 columns) and save them under `notebooks/data/`.
4. Optionally, create a pre-joined benchmark `indoor_pollution_vs_clean_fuels_2021.csv` for comparison.
5. Re-run the notebook; it will load from these local files.

