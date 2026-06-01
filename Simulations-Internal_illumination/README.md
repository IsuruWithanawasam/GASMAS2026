# GASMAS — Internal Illumination: Monte Carlo Simulations

Monte Carlo photon transport simulations for **GASMAS (Gas in Scattering Media Absorption Spectroscopy)** in internal illumination mode (isotropic diffusing probe source inside thorax tissue). Simulations sweep chest-wall thickness and source–detector separation (SDS) to characterise air pathlength distributions and GASMAS signal strength.

Built on [pmcx](https://github.com/fangq/pmcx) (GPU-accelerated MCX) and run on Google Colab (T4 GPU).

---

## Notebooks

| Notebook | Purpose |
|---|---|
| `Monte_Carlo-Internal.ipynb` | Run MCX simulations across chest-wall thicknesses; save per-thickness `.npz` output files |
| `GASMAS_Simulation-Internal.ipynb` | Compute GASMAS signal (S, T normalisation) from saved photon data using GPU-accelerated Beer–Lambert weighting |
| `Photon_Count_Fluence-Internal.ipynb` | Analyse detected photon counts and fluence volumes across thickness/SDS sweep |

---

## Requirements

```
pmcx
hitran-api
numpy
scipy
matplotlib
torch        # for GASMAS signal notebook (GPU path)
```

Install in Colab:

```bash
pip install pmcx hitran-api numpy scipy matplotlib torch -q
```

---

## Usage

### 1. Run Monte Carlo simulations

Open `Monte_Carlo-Internal.ipynb`. Set the geometry parameters at the top of the configuration cell:

```python
CW_THICKNESSES_MM = [10, 15, 20, 25, 30, 35]   # chest-wall thicknesses to sweep
SDS_LIST_MM       = [5, 10, 15, 20, 25, 30, 35] # source–detector separations
```

Run all cells. For each thickness, the notebook launches an MCX simulation and saves the detected-photon array and fluence volume to Google Drive:

```
gasmas_internal_probe_results_10mm.npz
gasmas_internal_probe_results_15mm.npz
...
```

Each `.npz` contains:
- `detp` — detected photon array (partial path lengths per photon, per tissue layer)
- `fluence` — 3-D fluence volume

### 2. Compute GASMAS signal

Open `GASMAS_Simulation-Internal.ipynb`. Set `DATA_ROOT` to the Drive folder containing the `.npz` files. Run all cells to compute the GASMAS signal S (differential absorption signal) for each thickness and SDS using per-photon Beer–Lambert weighting over the O₂ A-band (763.84 nm).

### 3. Analyse photon count and fluence

Open `Photon_Count_Fluence-Internal.ipynb`. Point `DATA_ROOT` to the same folder. Run all cells to generate plots of detected photon count and mean air pathlength as a function of SDS and chest-wall thickness.

---

## Google Drive setup

All three notebooks mount Google Drive and expect simulation outputs under a common root folder. Set this path near the top of each notebook:

```python
DATA_ROOT = '/content/drive/MyDrive/your_folder_here'
```

---

## Tissue geometry

The simulated geometry is a layered slab representing the adult thorax in internal illumination mode:

| Layer | Tissue |
|---|---|
| 1 | Thorax |
| 2 | Esophagus |
| 3 | Lung (air-filled scattering medium) |

Optical properties are set at 763 nm following Jacques (2013) empirical scaling. The isotropic source is placed inside the lung layer to mimic a diffusing probe.
