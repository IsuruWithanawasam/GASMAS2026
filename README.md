# Feasibility of Adult Lung Monitoring using GASMAS

**Development and Evaluation of Advanced Large-Geometry Phantoms**

Master's Thesis by Isuru Chanilka Mawella Withanawasam  
Division of Combustion Physics, Department of Physics, Lund University  
Erasmus Mundus Joint Master Programme in Large Scale Accelerators and Lasers (LASCALA), 2026  

Supervisors: Anna-Lena Sahlberg (supervisor), Emilie Krite Svanberg (co-supervisor)

---

## Overview

This repository contains all simulation and analysis code written as part of the master's thesis investigating the feasibility of Gas in Scattering Media Absorption Spectroscopy (GASMAS) for non-invasive adult lung monitoring. The work combines Monte Carlo photon transport simulations of thorax geometries with experimental GASMAS measurements on gelatin-based tissue-mimicking phantoms.

---

## Repository structure

```
├── Experimental_Data_Analysis/
├── Simulations-External_illumination/
├── Simulations-GASMAS_calibration/
└── Simulations-Internal_illumination/
```

### `Experimental_Data_Analysis`
Signal extraction from wavelength-sweep measurements over the O₂ A-band (763.84 nm) on gelatin phantoms. Covers both illumination geometries. The pipeline performs Savitzky–Golay smoothing, polynomial baseline correction, and extraction of the normalised absorption peak `d = (b − s) / s₀` with full uncertainty propagation. See the folder README for details.

### `Simulations-External_illumination`
Monte Carlo photon transport simulations (pmcx/MCX) in external illumination mode — Gaussian beam at the tissue surface, reflectance geometry. Sweeps chest-wall thickness (10–35 mm) and source–detector separation (5–35 mm). Includes GASMAS signal computation and photon count/fluence analysis. See the folder README for details.

### `Simulations-GASMAS_calibration`
Pathlength distribution analysis comparing the 763 nm (O₂) and 935 nm (H₂O) channels across all source–detector separations. Assesses whether the two wavelengths probe the same lung volume, which is a prerequisite for using 935 nm as a pathlength calibration reference. See the folder README for details.

### `Simulations-Internal_illumination`
Monte Carlo simulations in internal illumination mode — isotropic source placed inside the lung layer to mimic a diffusing probe. Same thickness/SDS sweep as the external case. See the folder README for details.

---

## Requirements

All notebooks are written for Google Colab (T4 GPU). Notebooks using pmcx require GPU acceleration. Others can be run locally with minor path adjustments. Core dependencies:

```
pmcx
hitran-api
numpy
scipy
matplotlib
torch
scikit-learn
pandas
```

---

## Citation

If you use this code, please cite the thesis:

> Isuru Chanilka Mawella Withanawasam, *Feasibility of Adult Lung Monitoring using GASMAS: Development and Evaluation of Advanced Large-Geometry Phantoms*, Master's Thesis, Lund University, 2026.
