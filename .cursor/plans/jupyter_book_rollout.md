# Jupyter Book Rollout

## Goal

Launch a minimal Jupyter Book containing the 4 workshop notebooks with pre-executed outputs so students access one URL without Colab path setup.

## Scope

- Include only:
  - `notebooks/01_line_chart_clean_fuels.ipynb`
  - `notebooks/03_line_chart_death_rate.ipynb`
  - `notebooks/05_bubble_chart_clean_fuels_vs_gdp.ipynb`
  - `notebooks/06_optional_join_bubble.ipynb`
- Pre-executed output display for reliability.
- Minimal docs structure only (no extra chapters yet).

## Files to create/update

- `docs/_config.yml`
- `docs/_toc.yml`
- `docs/intro.md`
- `README.md` (short Jupyter Book entrypoint)
- `.cursor/plans/jupyter_book_rollout.md` (this plan + status board)

## Implementation steps

1. Create minimal `docs/` scaffold (`_config.yml`, `_toc.yml`, `intro.md`).
2. Wire TOC in teaching order: 01 -> 03 -> 05 -> 06.
3. Configure book to use pre-executed notebook outputs.
4. Build locally and resolve any rendering/build issues.
5. Add concise publish instructions for GitHub Pages URL.
6. Update README with "open the book" and local build instructions.

## Status Board

- `Book scaffold` - Create `docs/_config.yml`, `docs/_toc.yml`, `docs/intro.md` - `Done`
- `Notebook inclusion` - Add notebooks 01/03/05/06 in TOC order - `Done`
- `Execution mode` - Ensure pre-executed output display - `Done`
- `Local build` - `jupyter-book build docs/` passes - `Done`
- `Navigation QA` - Verify order/titles/readability - `Done`
- `Publish path` - Add GitHub Pages deployment instructions - `Done`
- `README update` - Add Jupyter Book usage section - `Done`

## Success criteria

- Book builds successfully from `docs/`.
- All 4 notebooks are viewable from one navigable site.
- One stable URL can be shared with students.
- Students can consume content without local/Drive path setup.
