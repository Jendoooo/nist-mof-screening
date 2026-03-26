# ZIF-8 CO₂/CH₄ Separation: A Reproducibility-Graded Computational Pipeline

A reproducible, open-source workflow for evaluating metal-organic framework (MOF) performance in gas separation — from raw NIST database data to validated IAST mixture predictions.

Built as part of an MSc research project at the University of Strathclyde (2025), this pipeline addresses a known problem in MOF adsorption research: up to 20% of published isotherm data contain outliers that can significantly distort computational predictions ([Park et al., 2017](https://doi.org/10.1021/acs.chemmater.7b04287)).

---

## What this does

Starting from nothing but a material name, this pipeline:

1. **Collects** pure-component adsorption isotherms from the [NIST/ARPA-E Adsorption Database](https://adsorption.nist.gov/isodb) via the official GitHub repository
2. **Validates** raw data manually — filtering to experimental-only measurements, standardising units, removing GCMC-calibrated and simulation-derived entries
3. **Screens** for reproducibility using a statistical IQR-based flagging framework adapted from Park et al. (2017), assigning an R-grade (R1 = Excellent, R2 = Good, etc.)
4. **Constructs** consensus isotherms from the screened data with 95% confidence intervals
5. **Fits** the Tóth isotherm model — selected for its physical relevance (surface heterogeneity) and superior numerical fit (R² > 0.999) over Langmuir, Quadratic, Henry's Law, and Temkin alternatives
6. **Predicts** binary mixture adsorption using Ideal Adsorbed Solution Theory (IAST) across a full pressure (1–50 bar) and composition (yCO₂ = 0.01–0.99) sweep
7. **Benchmarks** IAST predictions against ideal pure-component selectivity and available experimental binary data

Applied here to **ZIF-8** (a zeolitic imidazolate framework) for **CO₂/CH₄ separation** — relevant to natural gas sweetening and biogas upgrading.

---

## Key results

- Both ZIF-8 CO₂ and CH₄ datasets achieved **R1 (Excellent Reproducibility)** — outlier fractions of 7% (CH₄) and 7% (CO₂)
- CO₂ is **consistently and preferentially adsorbed** over CH₄ across all conditions
- IAST selectivity **increases with pressure** (3.5 at 1 bar → 4.9 at 50 bar) — unusual and operationally advantageous
- IAST significantly **outperforms ideal selectivity** at high pressures, diverging by up to 60% — confirming competitive exclusion effects that pure-component models miss
- At high pressure, IAST **underestimates** total CO₂ uptake relative to the sole available binary experimental dataset — attributed to ZIF-8's known **linker-rotation swing effect**, which creates a new adsorption site (Site IV) that rigid IAST cannot model

---

## Repository structure

```
nist-mof-screening/
├── notebooks/
│   └── ZIF8_CO2CH4_Full_Pipeline.ipynb   Full pipeline — data to results
├── data/
│   ├── co2_screened_isotherms.csv         Tóth-fitted CO₂ isotherms + consensus stats
│   ├── ch4_screened_isotherms.csv         Tóth-fitted CH₄ isotherms + consensus stats
│   ├── iast_sweep_results.csv             Pre-computed IAST sweep (pyIAST, 256 rows)
│   └── binary_experimental.csv            Chmelik binary experimental data
├── figures/
│   ├── all_isotherms_combined_labeled_final.png
│   ├── co2_full_A.png
│   ├── co2_zoom_B.png
│   ├── ch4_full_C.png
│   └── ch4_zoom_D.png
├── requirements.txt
└── README.md
```

---

## Getting started

### Requirements

```bash
pip install -r requirements.txt
```

### Run the pipeline

Open and run `notebooks/ZIF8_CO2CH4_Full_Pipeline.ipynb` sequentially.

The notebook is self-contained. Data is fetched directly from the NIST ISODB GitHub repository — no local database download required.

---

## Data source

All isotherm data sourced from the **NIST/ARPA-E Database of Novel and Emerging Adsorbent Materials**:

> NIST Standard Reference Database 205. Available at: https://adsorption.nist.gov/isodb
> GitHub mirror: https://github.com/NIST-ISODB/isodb-library

---

## Methodology

### Reproducibility screening

Adapted from the framework introduced by Park, Howe & Sholl (2017):

> Park, J., Howe, J. D., & Sholl, D. S. (2017). How Reproducible Are Isotherm Measurements in Metal–Organic Frameworks? *Chemistry of Materials*, 29(24), 10487–10495. https://doi.org/10.1021/acs.chemmater.7b04287

Isotherms are interpolated onto a common logarithmic pressure grid. At each grid point, the IQR is computed across all screened isotherms. Points falling outside 1.5×IQR are flagged. Isotherms accumulating ≥4 flags are classified as outliers and excluded from consensus construction.

### IAST

Binary mixture predictions computed using the Ideal Adsorbed Solution Theory (Myers & Prausnitz, 1965), implemented via [pyIAST](https://github.com/CorySimon/pyIAST) with a custom `TothIsotherm` class providing the analytical spreading pressure integral for the Tóth model.

### Tóth model

Selected over Langmuir, Quadratic, Henry's Law, and Temkin alternatives based on:
- RMSE: CO₂ = 0.0134, CH₄ = 0.0172
- R²: CO₂ = 0.99998, CH₄ = 0.99984
- Physical basis: accounts for surface heterogeneity in ZIF-8

---

## Citation

If you use this workflow, please cite:

> Ayeola, O. A.-H. (2025). *Identifying Future Carbon Capture Materials*. MSc Research Project, Department of Chemical & Process Engineering, University of Strathclyde.

---

## Author

**Olajide Abdul-Hafeez Ayeola**
MSc Sustainable Engineering: Chemical Processing (Distinction), University of Strathclyde, 2025
B.Eng Chemical Engineering (First Class), Afe Babalola University, 2020

[olajideayeola.com](https://olajideayeola.com)
