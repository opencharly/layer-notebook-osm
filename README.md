# notebook-osm

A standalone **marimo** notebook for the OpenStreetMap analytics + vector-tile
pipeline, shipped as a charly *data layer* and seeded into `/workspace/notebooks/`
at deploy time.

The `notebook-osm` candy ships exactly one file —
`osm-monaco-viz.py`, a self-contained marimo notebook. It has no packages, no
services and no baked DAGs: the notebook **self-authors its own Airflow DAGs at
runtime**, triggers them via the Airflow REST API, runs Polars analysis on the
resulting GeoParquet, and renders maps.

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `notebook-osm` |
| Type | Data-only — one notebook file, no packages, no services |
| Volume | `workspace` → `/workspace` (supplied by the jupyter base) |
| Data | `data/notebooks/` → `workspace` volume, dest `notebooks` |
| Notebook | `/workspace/notebooks/osm-monaco-viz.py` |

## What the notebook does

The notebook's cells, in source order:

1. Resolve and display the seven URL environment variables (server-side
   `AIRFLOW_API_INTERNAL_URL` vs browser-bound `MARTIN_PUBLIC_URL`) as one
   diagnostic cell.
2. Self-author **six** Airflow DAG files into `${AIRFLOW_DAGS_DIR}` — the OSM
   pipeline plus GTFS, gpq-tiles, DuckDB MVT, DuckDB freestiler, and Shortbread.
3. Trigger every DAG in parallel and poll each to success.
4. Analyze the GeoParquet with GPU Polars (`cudf-polars-cu13`), pyarrow,
   DuckDB Spatial, `polars-st` and `geopolars`.
5. Render **two** maps: the streets map via MapLibre GL JS + martin vector tiles
   + 3D terrain, and a transit map via folium with 98 bus-stop markers.

The two URL spaces are load-bearing: the marimo kernel runs inside the pod and
reaches Airflow at `AIRFLOW_API_INTERNAL_URL`, while the browser runs on the host
and reaches tiles at `MARTIN_PUBLIC_URL`. Mixing them is the most common failure
mode.

## How to use it

Compose the layer in a box's `candy:` list — the `versa` image does exactly this:

```yaml
versa:
  candy:
    base: cachyos
    candy:
      - '@github.com/opencharly/layer-notebook-osm:v2026.240.0121'
      # ... marimo, airflow, osm-tools, and the versatiles layers
```

Deploy and open the notebook in marimo at `http://localhost:2718`; navigate to
`notebooks/osm-monaco-viz.py`.

## Layout

- `charly.yml` — the `notebook-osm:` candy entity (the `data:` mapping and the
  `plan:` checks) plus the embedded `skill:` entity.
- `data/notebooks/osm-monaco-viz.py` — the notebook.
- `README.md` — this user overview.

## Related

- Owning skill: `/charly-versa:notebook-osm` — the notebook content, the
  dual-DAG self-authoring pattern, the two URL spaces, and the surfaced-and-fixed
  bug catalog.
- Runtime: `/charly-versa:marimo-layer`, `/charly-versa:airflow-layer`,
  `/charly-versa:osm-tools-layer`.
- Composing image: `/charly-versa:versa`.
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella.
