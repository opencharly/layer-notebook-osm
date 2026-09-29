# AGENTS.md — layer-notebook-osm

Standalone candy repo for the `notebook-osm` data layer — a single self-contained
marimo notebook (`osm-monaco-viz.py`) that self-authors its own Airflow DAGs at
runtime, runs Polars analysis on OSM + GTFS data, and renders two maps. The candy
lives in `charly.yml` at the repo root: the `data:` mapping into the `workspace`
volume, the `plan:` `check:` assertions, and the embedded `skill:` entity
projected into the marketplace corpus as `/charly-versa:notebook-osm`.

Canonical files:

- `charly.yml` — the `notebook-osm:` candy entity and the `notebook-osm-skill:`
  skill entity.
- `data/notebooks/osm-monaco-viz.py` — the notebook.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-versa:notebook-osm` — the owning skill. The notebook content, the
  dual-DAG self-authoring pattern, the server-side vs browser-bound URL spaces,
  the MapLibre/folium rendering split, and the bug catalog. Load before editing
  or troubleshooting.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, the `data:` field, and service
  declarations). Load before editing any entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` steps are the functional evidence — they assert
  the `notebooks` subdirectory is provisioned and the notebook file is present.
  A change to the notebook or its path must keep those checks honest.

## Modify this repo

- Edit the `notebook-osm:` candy entity AND the `notebook-osm-skill:` skill
  entity in `charly.yml` together. The skill is the projected usage source, so a
  data, path, or behaviour change not mirrored in the skill leaves the corpus
  stale.
- The notebook is a data-only artifact: no packages or services belong in this
  candy. Runtime dependencies (marimo, Airflow, martin, the Polars stack) are
  owned by the composing image's candies.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo — read it
  before landing.
- Release history lives in `CHANGELOG/`.
