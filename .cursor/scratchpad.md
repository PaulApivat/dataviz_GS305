# Scratchpad

## Background and Motivation

GS305 Data Visualization workshop materials centered on recreating Our World in Data (OWID) charts and teaching tidy data principles.\n
- Use Jupyter/Colab notebooks so students can run and modify code themselves.\n- Match OWID aesthetics (line, bar, bubble charts) for a small set of indicators on access to clean fuels and indoor air pollution.\n- Optionally extend the material into a Jupyter Book so students can review the full class (slides + notebooks) in the browser.

## Key Challenges and Analysis

- Recreating OWID charts accurately requires careful handling of filters (countries, years), indicator definitions, and units.\n- Ensuring notebooks are Colab-friendly (no local paths, minimal dependencies) while still being clear and pedagogical.\n- Demonstrating tidy data and joins in a way that is conceptually simple for students but technically correct (joining multiple OWID datasets by entity/year).\n- Keeping repo structure, Cursor rules, and skills aligned so future changes remain organized and easy to extend (e.g., adding new charts or converting to Jupyter Book).

## High-level Task Breakdown

- Set up project structure and metadata\n  - Create `notebooks/`, `notebooks/data/`, and `slides/` directories.\n  - Define dependencies (`requirements.txt`) for pandas, matplotlib, and plotly.\n  - Document overall plan in `.cursor/plans/owid_workshop_implementation.md`.\n- Scaffold Cursor workflow\n  - Create Cursor Rules for Planner/Executor roles and scratchpad usage.\n  - Create `.cursor/scratchpad.md` with standard sections.\n  - Add reusable skills for this repo (`owid-chart-recreator`, `workshop-notebook-architect`, `owid-tidy-join-coach`).\n- Implement core OWID chart notebooks\n  - 01–04: line and bar charts for clean fuels and indoor air pollution death rate.\n  - 05: bubble chart of clean fuels vs GDP (with interactive Plotly version).\n  - 06: optional join notebook turning two line datasets into a bubble (death rate vs clean fuels).\n- Prepare for optional Jupyter Book\n  - Add `docs/` with `_config.yml`, `_toc.yml`, and intro page.\n  - Integrate notebooks and slides PDF into a coherent online resource.

## Project Status Board

- Setup & infra
  - ✅ Folders: `notebooks/`, `notebooks/data/`, `slides/` created.
  - ✅ Dependencies: `requirements.txt` created with pandas, matplotlib, plotly.
  - ⬜ Data URLs: need to pin OWID CSV URLs for all charts and note join keys for 06.
  - ⬜ Shared style: OWID-like matplotlib defaults to be implemented and reused.
- Notebooks
  - ⬜ 01–06 notebooks not yet created; structure and chart specs defined in plan.
- Cursor workflow
  - ✅ Cursor rules file for Planner/Executor.
  - ✅ Scratchpad created with standard sections.
  - ✅ Three repo-specific skills created under `.cursor/skills/`.

## Current Status / Progress Tracking

- Plan and repo scaffolding
  - OWID workshop implementation plan written and committed.
  - Folders for notebooks and slides created.
  - Cursor rules and scratchpad in place; skills drafted and stored.
- Outstanding next actions (high level)
  - Start implementing notebook 01 following the plan and skills.
  - Resolve OWID CSV URLs and join logic, especially for notebooks 05 and 06.

## Executor's Feedback or Assistance Requests

- When execution begins on notebooks, confirm:\n  - Preferred library versions for `requirements.txt` (pinned vs loose versions for Colab).\n  - Whether to store local CSV snapshots in `notebooks/data/` as a backup to OWID URLs.\n- Before building a Jupyter Book, confirm hosting preference (e.g. GitHub Pages) and whether slides should be embedded or linked.

## Lessons

- Use `.cursor/plans/owid_workshop_implementation.md` as the single source of truth for chart list, notebook paths, and implementation order.\n- Keep notebooks simple and explicit for students; avoid over-abstracting data transformations.\n- Maintain OWID-style citations and units consistently across charts.\n- Update the Status Board in the plan whenever structural changes (folders, notebooks, major milestones) occur.