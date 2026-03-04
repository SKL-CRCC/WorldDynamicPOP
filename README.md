# WorldDynamicPOP

A two-stage pipeline for generating and spatially downscaling dynamic (time-varying) population grids, combining AI-generated trajectory data with building morphology and land-use information.

## Overview

Accurate, high-resolution dynamic population data is essential for urban planning, disaster response, and transportation analysis. **WorldDynamicPOP** addresses this need through a two-stage approach:

1. **Dynamic Population Generation** — Estimates hourly population distribution at 1 km resolution by fitting a power-law model between visit density and census population, following the methodology of Deville et al. (2014) and Liu et al. (2018).

2. **Spatial Downscaling** — Refines the 1 km estimates to 100 m resolution by distributing population according to Effective Housing Population (EHP) weights derived from building footprints, floor counts, and land-use categories.

## Project Structure

```
WorldDynamicPOP/Scripts/
├── Step1-Dynamic_Population/
│   ├── visit_time_extract.py              # Extract visit matrix [48 × n_grids] from trajectories
│   └── generate_dynamic_pop.py            # Power-law model fitting & dynamic population generation
│
├── Step2-Population_Downscaling/
│   ├── Land_cover_retrieval_and_processing.py # Land-cover extraction & classification
│   ├── Building_retrieval_and_processing.py   # Building extraction, POI linking & classification
│   ├── Building_add_height.py                 # Fill missing building heights
│   └── Dynamic_population_downscaling.py      # EHP-based downscaling (1 km → 100 m)
│
├── requirements.txt
└── README.md
```

---

## Usage

The pipeline runs in order:

```bash
pip install -r requirements.txt

# Step 1: Dynamic Population Generation
python Step1-Dynamic_Population/visit_time_extract.py
python Step1-Dynamic_Population/generate_dynamic_pop.py

# Step 2: Population Downscaling
python Step2-Population_Downscaling/Land_cover_retrieval_and_processing.py
python Step2-Population_Downscaling/Building_retrieval_and_processing.py
python Step2-Population_Downscaling/Building_add_height.py
python Step2-Population_Downscaling/Dynamic_population_downscaling.py
```

## Data Sources

| Dataset | Purpose | Link |
|---------|---------|------|
| **WorldMove** | Synthetic mobility trajectories for 1 600+ cities | https://fi.ee.tsinghua.edu.cn/worldmove/ |
| **Overture Maps** | Building footprints, POIs, and land-use data | https://overturemaps.org/ |
| **3D-GloBFP** | Global building height data | https://zenodo.org/records/15459025 |

## Methodology

### Stage 1 — Dynamic Population Estimation

Following Deville et al. (2014), nighttime (3:00–4:00 AM) visit density is used to calibrate a power-law relationship with census population:

$$\rho_c = \alpha \cdot \sigma_c^{\beta}$$

where $\sigma_c$ is the visit density and $\rho_c$ is the population density. The calibrated model is then applied to all 48 half-hour time slots, with each slot's total rescaled to match the census total.

### Stage 2 — Spatial Downscaling

Population in each 1 km cell is distributed to 100 m sub-cells proportional to the Effective Housing Population (EHP):

$$Pop_{100m} = Pop_{1km} \times \frac{EHP_{100m}}{\sum EHP_{100m \in 1km}}$$

$$EHP = \sum \left( W_{time} \cdot W_{indoor} \cdot S_{eff} \right)$$

where $W_{time}$ is the hourly time-use weight by land-use category, $W_{indoor}$ is the indoor/outdoor proportion factor, and $S_{eff}$ is the effective floor area computed from building footprints and floor counts.

---

## Citation

If you use this code in your research, please cite:

```bibtex
@software{WorldDynamicPOP,
  title  = {WorldDynamicPOP},
  author = {Zhiyong LONG, Ruiyi YANG, Huanfeng DUAN},
  year   = {2026},
  url    = {https://github.com/SKL-CRCC/WorldDynamicPOP}
}
```

## License

Apache 2.0.


