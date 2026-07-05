# ADB Road Safety Assessment and Safe System Speed Review

This project evaluates road-segment safety conditions in Thailand and Maharashtra using road geometry, intersection density, surrounding land-use context, points of interest (POI), traffic exposure, observed P85 speed, posted speed limit, and Safe System target speed.

The workflow produces:
- Road-level POI and built-up-area context
- POI/GHSL surrounding-context clustering
- Target speed limits based on surrounding context and intersection density
- Posted-speed-limit and Safe System speed misalignment assessments
- VRU exposure assessment
- A total road-risk score from 0 to 100
- Interactive HTML maps for Thailand and Maharashtra

---

## 1. Input Files

Upload the following files into **local Google Colab storage**:

```text
ADB_Innovation_Maharashtra.geojson
ADB_Innovation_Thailand.geojson
```

The notebook expects these files at:

```text
/content/ADB_Innovation_Maharashtra.geojson
/content/ADB_Innovation_Thailand.geojson
```

> Do not upload the files only to Google Drive unless you update the notebook paths. The notebook is configured to use local Colab storage.

---

## 2. Run the Notebook in Order

Run the notebook sequentially. Do not skip a step or run later cells before the prior output has been created.

### Cell 1 — Package Installation and Imports

Run the package installation and import cell first.

The notebook uses packages including:

```text
geopandas
pyogrio
shapely
fiona
osmium
rasterio
scikit-learn
scipy
joblib
folium
```

### Cell 2 — Folder and File Paths

Run the folder-path configuration cell.

The workflow uses these output folders:

```text
/content/1. Ready to Use
/content/2. Add Derived Intersection Data
/content/3. OSM POI Data
/content/4. GHSL Built-Up Context
/content/5. POI and GHSL Context Clustering
/content/6. Final Result
```

---

## Step 1 — Prepare and Merge Road Data

This step:
- Loads the Thailand and Maharashtra GeoJSON files
- Standardises the source structure
- Creates a unique `analysis_segment_id`
- Separates valid and excluded road records
- Saves the merged valid road dataset

Output:

```text
/content/1. Ready to Use/ADB_Innovation_merge_valid.gpkg
```

---

## Step 2 — Add Intersection Metrics

This step calculates road-level intersection indicators, including:

```text
intersection_count_per_km
```

Output:

```text
/content/2. Add Derived Intersection Data/ADB_Innovation_merge_valid_2.gpkg
```

---

## Step 3 — Add OSM POI Metrics

This step calculates nearby POI exposure for each road segment.

POI categories:
- Public transport
- Office
- School
- Commercial
- Industrial

The analysis includes POI count, distance, and density metrics around each road segment.

Output:

```text
/content/3. OSM POI Data/Road Segment POI Metrics/ADB_Innovation_merge_valid_3.gpkg
```

---

## Step 4 — Add GHSL Built-Up Context

This step evaluates built-up context around roads using GHSL data.

Key indicators:

```text
built_up_share_pct
built_up_continuity_pct
```

Output:

```text
/content/4. GHSL Built-Up Context/ADB_Innovation_merge_valid_4.gpkg
```

---

## Step 5 — POI and GHSL Context Clustering

This step clusters roads based on surrounding POI and built-up-area context.

The clustering helps distinguish road environments such as:
- Low-activity or open context
- Dense mixed-use urban context
- Urban commercial corridor
- Industrial or logistics fringe
- Motorway context

Output:

```text
/content/5. POI and GHSL Context Clustering/ADB_Innovation_merge_valid_5.gpkg
```

---

## Step 5A — Name the Clusters

This step asks the user to assign meaningful names to each cluster.

Example:

```text
Cluster A → Low-activity / open context
Cluster B → Dense mixed-use urban activity center
Cluster C → Urban commercial corridor
Cluster D → Industrial-adjacent / logistics fringe
Cluster Z → Motorway
```

The chosen name is saved in:

```text
poi_ghsl_context_cluster_name
```

---

## Step 6 — Define Target Speed Limits

This step asks the user to define:
- Initial speed limit by cluster
- Surrounding context: `rural-like` or `built-up`

The model classifies the road's intersection density and calculates:

```text
intersection_density
target_speed_limit
```

Intersection-density rules:

```text
Rural-like:
- Low: 0 intersections/km
- Moderate: >0 and <1 intersections/km
- High: >=1 intersections/km

Built-up:
- Low: <2 intersections/km
- Moderate: >=2 and <6 intersections/km
- High: >=6 intersections/km
```

Output:

```text
/content/6. Final Result/ADB_Innovation_target_speed_limit.gpkg
```

---

## Step 7 — Final Assessment and Total Risk Scoring

This step performs the final road-safety assessment.

### Posted Speed Limit Assessment

```text
p85_speed - speed_limit
```

Output fields:

```text
posted_limit_misalignment
posted_limit_misalignment_point
```

### VRU Exposure Assessment

The user provides a baseline VRU point for each cluster.

The score is adjusted based on proximity to:
- Public transport
- Office
- School
- Commercial POIs

Output fields:

```text
vru_baseline_point
vru_poi_proximity_point
vru_point
```

The final VRU score is capped at `25`.

### Safe System Speed Assessment

```text
p85_speed - target_speed_limit
```

Output fields:

```text
safe_system_misalignment
safe_system_misalignment_point
safe_system_misalignment_insight
```

### Intersection Density Assessment

Output field:

```text
intersection_density_point
```

### Traffic Exposure Assessment

Traffic exposure is based on:

```text
ranked_percentile
```

Output fields:

```text
traffic_exposure
traffic_exposure_point
```

### Total Risk Score

```text
total_risk_point =
posted_limit_misalignment_point
+ vru_point
+ safe_system_misalignment_point
+ intersection_density_point
+ traffic_exposure_point
```

Maximum score:

```text
100 points
```

Output:

```text
/content/6. Final Result/ADB_Innovation_target_speed_limit_with_scoring.gpkg
```

---

## Step 8 — Interactive Total Risk Maps

This step creates two interactive HTML maps:

```text
/content/6. Final Result/thailand_total_risk_map.html
/content/6. Final Result/maharashtra_total_risk_map.html
```

Road colour is based on `total_risk_point`:

```text
0   → Green
50  → Yellow
100 → Red
```

The maps include a checkbox filter using:

```text
percentile_band
```

Each road popup includes:

```text
analysis_segment_id
road_name
cluster_name
shape_length
ranked_percentile
percentile_band
speed_limit
target_speed_limit
p85_vs_posted_limit
p85_vs_target_speed_limit
intersection_density
traffic_exposure
safe_system_misalignment
total_risk_point
```

---

## 3. Final Outputs

The final dashboard-ready files are saved in:

```text
/content/6. Final Result/
```

Key outputs:

```text
ADB_Innovation_target_speed_limit.gpkg
ADB_Innovation_target_speed_limit_with_scoring.gpkg
cluster_vru_baseline_configuration.csv
road_safety_scoring_summary.csv
thailand_total_risk_map.html
maharashtra_total_risk_map.html
```

---

## 4. Important Notes

- Run the notebook in sequence from Cell 1 through Step 8.
- Some steps require manual user input, including cluster names, initial speed limits, surrounding-area context, and baseline VRU points.
- The final maps are interactive HTML files and can be opened in a browser.
- Use the `percentile_band` checklist in the upper-right map control to show or hide each percentile band.
- A missing, zero, or negative posted speed limit is treated as `80 km/h` and marked using:

```text
speed_limit_imputed = True
```
