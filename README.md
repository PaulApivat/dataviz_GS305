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
