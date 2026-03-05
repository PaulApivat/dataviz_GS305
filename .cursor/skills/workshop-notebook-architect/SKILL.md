# Workshop Notebook Architect

## Purpose

Design and maintain Jupyter notebooks in this repo so they are consistent, pedagogically clear, Colab-friendly, and aligned with the OWID workshop plan and Jupyter Book goals.

## When to Use

- Creating a new workshop notebook under `notebooks/`.
- Refactoring or cleaning up an existing notebook.
- Preparing notebooks for students to run in Google Colab.
- Getting notebooks ready to be included in a Jupyter Book.

## Relevant Repo Context

- Plan and status:
  - `.cursor/plans/owid_workshop_implementation.md`
    - Notebook list and paths
    - Implementation order
    - Status Board
- Notebooks directory:
  - `notebooks/*.ipynb`
- Future book:
  - `docs/` (to be added later)
- Slides:
  - `slides/` (e.g. `slides/dataviz_lecture.pdf`)

## Behavior and Guidelines

1. **Follow the plan**
   - Before editing notebooks, read `.cursor/plans/owid_workshop_implementation.md`.
   - Use:
     - The **implementation order** to decide which notebook to work on.
     - The **Status Board** to track progress (`Not started` → `In progress` → `Done`).
   - Keep notebook names and paths consistent with the plan.

2. **Standard notebook structure**
   - Each workshop notebook should follow this high-level structure:
     1. **Title and context (Markdown):**
        - Clear title (matching the chart/topic).
        - A sentence on what students will learn (e.g. “We will recreate the OWID line chart on access to clean fuels for cooking and discuss tidy data.”).
     2. **Imports:**
        - `pandas`, `matplotlib.pyplot`, and optionally `plotly` or other libraries.
     3. **Data loading:**
        - From OWID URLs (`pd.read_csv`) or small CSVs in `notebooks/data/`.
        - No machine-specific paths.
     4. **Exploratory inspection:**
        - `head()`, `info()`, simple summaries to connect to tidy data concepts.
     5. **Data preparation:**
        - Filtering, joining, reshaping (melt/pivot) with comments where needed for learning.
     6. **Visualization:**
        - The primary chart(s), using a consistent OWID-like style.
        - For some notebooks, a second variant (e.g. interactive Plotly scatter).
     7. **Takeaways (optional but encouraged):**
        - Short markdown summarizing what the chart shows and which concepts it reinforces (e.g. tidy data, joins, log scales).

3. **Colab and portability**
   - Ensure each notebook:
     - Can be run from top to bottom with “Run all” in Colab.
     - Uses only:
       - URLs (e.g. OWID CSVs).
       - Or paths relative to the notebook directory (e.g. `data/clean_fuels.csv`).
   - Avoid:
     - `~/Desktop/...` or other absolute paths.
     - Dependencies that are unlikely to be available in Colab without clear install cells.

4. **Pedagogy and readability**
   - Prioritize **clarity over cleverness**:
     - Prefer explicit steps and intermediate variables instead of deeply nested one-liners.
     - Use meaningful variable names (e.g. `df_clean_fuels_2023`).
   - Use comments sparingly but purposefully:
     - Explain *why* a step is important, especially when teaching tidy data and joins.
   - Keep cells focused:
     - Avoid cells that do many unrelated things (e.g. loading, cleaning, plotting, and printing all at once).
     - Instead, separate into small, logically grouped cells.

5. **Consistency across notebooks**
   - Maintain a shared “visual language”:
     - Similar font choices, color palette, and grid usage.
     - Consistent placement of titles and source lines.
   - Use consistent naming for:
     - Notebooks (01–06 prefix as planned).
     - Dataframe variables where it aids comprehension (e.g. `df_clean`, `df_deaths`, `df_merged`).
   - If you introduce a new pattern (e.g. a better way to show sources or titles), consider updating the other notebooks to match, or note it in the plan.

6. **Jupyter Book readiness**
   - When editing notebooks, keep a future Jupyter Book in mind:
     - Avoid gigantic cell outputs that will bloat the book.
     - Do not embed large binary data in notebooks.
     - Keep metadata simple; avoid storing execution counts that depend heavily on local runs (not a hard requirement, but nice to clean before publishing).
   - Use headings and markdown that will make sense as a linear reading experience (e.g. “## Loading the Data”, “## Cleaning and Preparing”, “## Visualizing”).

7. **Updates to plan and Status Board**
   - When you:
     - Create a new notebook, or
     - Make a major structural or pedagogical change to an existing notebook
   - Then:
     - Suggest an update to `.cursor/plans/owid_workshop_implementation.md`:
       - Adjust the description if needed.
       - Update Status Board entries for that notebook.
       - Add notes about any new dependencies or patterns introduced.

8. **What not to do**
   - Don’t silently change notebook names or locations without also updating:
     - The plan.
     - Any documentation or links that reference them (e.g. future Jupyter Book TOC).
   - Don’t introduce highly abstract helper functions or class hierarchies that obscure the teaching goals.
   - Don’t assume students have context from other projects; each notebook should stand reasonably on its own in the workshop sequence.

## Interaction Pattern

When this skill is active and the user asks to add or modify a notebook:

1. Read `.cursor/plans/owid_workshop_implementation.md` and locate the relevant notebook(s).
2. Propose or adjust the notebook structure according to the guidelines above.
3. Ensure paths and code are Colab-friendly.
4. Briefly summarize for the user how the notebook:
   - Fits into the workshop flow.
   - Reinforces key lecture concepts (e.g. tidy data, joins, visualization choices).
5. Suggest any necessary updates to the plan’s Status Board.
