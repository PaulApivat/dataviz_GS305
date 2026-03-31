# dataviz
Data Visualization foundations (part of GS305 – Professional &amp; Personal Development Program @ SGS)

## Data sources

- Primary data and charts from [Our World in Data](https://ourworldindata.org/), especially:
  - Access to clean fuels for cooking (WHO Global Health Observatory – processed by OWID).
  - Death rate from indoor air pollution (IHME Global Burden of Disease – processed by OWID).
- All data, visualizations, and code from Our World in Data are open access under Creative Commons BY, and original data providers retain their own license terms.

In the workshop notebooks, each chart includes:

- A short **source line** (e.g. “Source: World Health Organization – Global Health Observatory (2025), processed by Our World in Data”).
- A brief **licensing and attribution** note where appropriate, following the guidance on the OWID “Reuse this work” sections.

## Workshop + Colab (Shared Google Drive)

The workshop notebooks are designed to run in Google Colab using snapshot CSV files stored under the local `notebooks/data/` folder. If the notebooks are stored on a **Shared Google Drive**, Colab must also be able to access that shared folder.

### First mounting (Colab)

Add this near the top of the notebook (or run it once per Colab session):

```python
from google.colab import drive
drive.mount("/content/drive", force_remount=True)
```

### Second: add the shared folder as a shortcut (required for visibility)

Colab can only see files that are accessible to the **account used in `drive.mount(...)`**.

Fastest fix path:
1. In Google Drive (web), go to **Shared with me**.
2. Find the folder from the instructor.
3. Right-click it → **Organize → Add Shortcut**.
4. Add it to **My Drive** (or `My Drive/Colab Notebooks`).
5. Back in Colab, re-run:
   ```python
   from google.colab import drive
   drive.mount("/content/drive", force_remount=True)
   ```
6. Re-run a quick check:
   ```python
   import os
   print(os.listdir("/content/drive/MyDrive"))
   ```
7. Then set the working directory to your notebooks folder, for example:
   ```python
   import os

   base = "/content/drive/MyDrive/GS305 - Dr Paul's session /notebooks"
   os.chdir(base)

   print("cwd:", os.getcwd())
   print("here:", os.listdir())
   print("data files:", os.listdir("data"))
   ```

Once `os.chdir(base)` points to the `notebooks/` folder, the notebook-relative paths like `data/clean_fuels.csv` will resolve correctly.

## Jupyter Book

The repository includes a minimal Jupyter Book in `docs/` that publishes the four workshop notebooks as a single student-facing website.

- `docs/intro.md`
- `docs/01_line_chart_clean_fuels.ipynb`
- `docs/03_line_chart_death_rate.ipynb`
- `docs/05_bubble_chart_clean_fuels_vs_gdp.ipynb`
- `docs/06_optional_join_bubble.ipynb`

### Open locally

```bash
source .venv/bin/activate
pip install -r requirements.txt
jupyter-book build docs/
open docs/_build/html/index.html
```

### Publish to GitHub Pages

1. Build the site:
   ```bash
   jupyter-book build docs/
   ```
2. Install deployment helper:
   ```bash
   pip install ghp-import
   ```
3. Publish the built HTML to `gh-pages`:
   ```bash
   ghp-import -n -p -f docs/_build/html
   ```
4. Share the Pages URL:
   - `https://<github-username>.github.io/<repo-name>/`

This gives students one stable URL without Colab path setup.

### Plotly interactivity (offline-safe)

The Plotly charts in the Jupyter Book are exported as self-contained HTML snippets with embedded Plotly JS. This means:

- no `npm install` is required
- no CDN dependency is required for chart interactivity
- hover/zoom/pan still work in the built pages
