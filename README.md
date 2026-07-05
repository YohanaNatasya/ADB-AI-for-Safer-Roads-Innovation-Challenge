# AI4SaferRoad

## Context-sensitive Safe Speed Alignment and Priority Screening

AI4SaferRoad reviews whether an ADB-provided reported posted speed limit is aligned with a context-sensitive Safe System target speed. It combines road-context clustering, intersection-conflict density, reported speed-limit alignment, P85 operating-speed alignment, vulnerable-road-user exposure proxies, and traffic exposure into a transparent **Total Risk Point**, ranging from 0 to 100.

This repository contains a Google Colab notebook that processes ADB road data for Thailand and Maharashtra, India, and produces auditable geospatial outputs and interactive maps.

## What the model does

- Uses K-means to group roads into interpretable settlement and activity contexts.
- Sets a Safe System target speed through a transparent cluster and intersection-density rule.
- Compares the reported posted speed limit and P85 speed separately against that target.
- Ranks segments using the Total Risk Point.
- Produces scored GeoPackages, audit CSVs, and interactive HTML maps.

## What the model does not do

- It does not verify the legal regulatory speed limit.
- It does not automatically mandate a speed-limit change.
- It does not use P85 to set the target speed.
- It does not interpret missing OSM POIs or missing infrastructure data as proof of low risk.

## Main outputs

- `ADB_Innovation_target_speed_limit_with_scoring.gpkg`
- `road_safety_scoring_summary.csv`
- `thailand_safe_system_assessment_map.html`
- `maharashtra_safe_system_assessment_map.html`
- K-means model, feature configuration, cluster-profile, and K-selection audit files

## How to run

1. Upload the ADB Thailand and Maharashtra GeoJSON files to Google Colab.
2. Open and run `ADB AI4SaferRoad Code v2.ipynb` sequentially.
3. When prompted, enter the submitted cluster labels, target-speed configuration, and baseline VRU points.
4. Review the generated audit CSVs before using the final map outputs.

## Submitted configuration

| Cluster | Name | Initial target speed | Context group | Baseline VRU points |
|---|---|---:|---|---:|
| A | Urban commercial strip | 50 km/h | Built-up | 15 |
| B | Dense mixed-use urban activity center | 30 km/h | Built-up | 20 |
| C | Transport-and-service interurban corridor | 70 km/h | Rural-like | 8 |
| D | Low-activity / open context | 70 km/h | Rural-like | 0 |
| E | Industrial-adjacent / logistics fringe | 70 km/h | Rural-like | 8 |
| F | School-adjacent open corridor | 70 km/h | Rural-like | 8 |
| G | School-and-commercial urban corridor | 30 km/h | Built-up | 20 |
| Z | Motorway | 100 km/h | Rule-based | 0 |

For the complete method, equations, scoring rules, limitations, and references, see [METHODOLOGY.md](METHODOLOGY.md).

## Data-use note

Do not publish ADB-provided source data, restricted derived data, or confidential outputs unless the challenge data-use agreement expressly permits it. The notebook and documentation can be shared with synthetic/example data where required.
