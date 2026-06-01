# GASMAS Experimental Signal Analysis

Signal extraction from wavelength-sweep measurements over the O₂ A-band (763.84 nm) for gelatin-based thorax phantoms. Two notebooks cover the two illumination geometries; the signal processing pipeline is identical in both.

| Notebook | Illumination mode | Phantom configurations |
|---|---|---|
| `GASMAS_Signal_Analysis_Internal_Illumination.ipynb` | Internal (diffusing probe inside phantom) | 45 mm and 60 mm chest-wall height; SDS 30–65 mm |
| `GASMAS_Signal_Analysis_External_Illumination.ipynb` | External (surface beam, reflectance) | ~20 mm and ~10 mm chest-wall; SDS 25–65 mm |

---

## Signal processing pipeline

Each raw measurement is a time-series voltage sweep as the laser current is scanned through the O₂ A-band. The pipeline extracts the normalised absorption peak from this sweep in five steps.

### 1. Truncation

The sweep record is clipped to a user-defined time window `[start_time, end_time]` that isolates a single forward scan. Window boundaries are set individually per dataset to exclude laser turn-on transients and any repeated scans.

### 2. Savitzky–Golay smoothing

A Savitzky–Golay filter (configurable window length and polynomial order) is applied to the truncated signal to suppress high-frequency detector noise while preserving the shape of the absorption feature. The smoothed signal `s` is carried forward to all subsequent steps.

### 3. Polynomial baseline fit

The O₂ absorption dip sits on a slowly varying background caused by laser intensity roll-off across the scan. A polynomial baseline `b(t)` is fitted to the smoothed signal using only the outermost `baseline_fit_fraction` of points at each end of the window — the central region containing the dip is excluded from the fit. This gives an estimate of what the signal would look like in the absence of gas absorption.

### 4. Baseline correction and normalisation

The corrected absorption signal is:

```
c(t) = b(t) - s(t)
```

The peak value `c` occurs at the dip index. The baseline value at that point, `s₀ = b(t_dip)`, is used to normalise:

```
d = c / s₀ = (b - s) / s₀
```

`d` is the normalised absorption peak reported for each dataset. It is dimensionless and proportional to the integrated O₂ column density along the photon path through the phantom.

### 5. Uncertainty propagation

The uncertainty on `d` is computed by propagating two independent contributions:

```
(σ_d / d)² = (σ_c / c)² + (σ_s₀ / s₀)²
```

- `σ_c` — measurement noise, estimated as the standard deviation of the residual between the raw and smoothed signal in the baseline regions only (uncontaminated by the dip).
- `σ_s₀` — uncertainty on the baseline value at the dip location, propagated analytically from the polynomial fit covariance matrix returned by `numpy.polyfit`.

---

## Key parameters

Each dataset has its own entry in the `PARAMS` dictionary:

```python
PARAMS = {
    '50mm': dict(
        start_time=0.015,
        end_time=0.027,
        savgol_window_length=75,
        savgol_polyorder=2,
        baseline_fit_fraction=0.25,
        baseline_poly_degree=3,
    ),
    ...
}
```

`baseline_fit_fraction=0.25` means the outermost 25% of points at each end are used for the baseline fit; the central 50% is treated as the dip region and excluded.

---

## Output

Each notebook produces:

- Three-panel diagnostic plots per dataset: raw + smoothed / smoothed + baseline fit / normalised signal.
- A summary plot of the normalised absorption peak `d` with error bars versus source–detector separation.

---

## Requirements

```
numpy
scipy
matplotlib
```
