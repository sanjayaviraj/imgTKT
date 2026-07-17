# imgTKT — A Quick Image Analysis Tool Kit

## How to use

**Use it online — nothing to install:** **[sanjayaviraj.github.io/imgTKT](https://sanjayaviraj.github.io/imgTKT)**

**OR** run it locally in 2 easy steps:

1. **Download** all files from this Google Drive link: **[download here](ADD_GOOGLE_DRIVE_LINK_HERE)**
2. **Open** `index.html` in your browser — no install, no server, nothing to set up.

---

Browser-based tools for microscopy and scientific image analysis. Each tool is a single self-contained
HTML file that runs entirely in your browser — **images are never uploaded anywhere**.

Inputs: multi-channel **TIFF / OME-TIFF** (channels auto-detected) and RGB **PNG / JPG**.

## Tools

| Tool | Purpose |
|------|---------|
| [Size Analysis](size_analysis.html) | Measure feature size (FWHM) from a line profile via Gaussian fitting |
| [Line Profile](line_profile.html) | Read the intensity profile of every channel along a drawn line |
| [ROI Inspector](roi_inspector.html) | Mean & sum intensity per channel inside a region |
| [Correlations](correlations.html) | Pearson & Manders colocalization between two channels |
| [Measure Angles](measure_angles.html) | Angle in degrees from three clicked points |
| [Particle Analyzer](particle_analyzer.html) | Otsu-threshold a channel and measure every object (area, shape, intensity) |
| [Plot Data](plot_data.html) | Plot exported CSV/TSV — scatter, line, bar, histogram, pie |

## Good to know

- **Accurate by design.** Brightness, LUTs, and normalization only change how the image *looks* — every measured and exported number comes from the **raw pixel data**.
- **Z-stacks & time series** are read from OME/ImageJ metadata, with a slider to scrub planes.
- **Records persist** across navigation (saved in your browser — only the measurements, never the image) and export to **CSV**.
- **Figures export** to PNG, JPG, or **SVG (vector)** at full resolution.
- **Colour-blind-safe** channel colours and microscopy LUTs (Fire, Viridis, Green Fire Blue).

## Running

Open `index.html` in any modern browser (Chrome, Firefox, Edge) — no server needed. Dependencies
(geotiff.js, Chart.js, UTIF.js, canvas2svg) load from CDNs at runtime; download them locally to run fully offline.

---

## How the measurements are computed

All formulas run on **raw pixel data**; display settings never affect the result.

**Size Analysis — FWHM.** The line profile is fit (Levenberg–Marquardt) to a Gaussian, and the full width at half maximum is reported in nm from the pixel size (fit quality as R²):

```
I(x) = offset + amplitude · exp( −(x − center)² / (2σ²) )
FWHM = 2·√(2·ln 2) · σ  ≈  2.3548 · σ
```

**ROI Inspector — mean & sum.** For a region of N pixels, per channel c:

```
sum_c  = Σ (raw intensity of channel c over the region)
mean_c = sum_c / N
```

Membership is tested at each pixel center (inside-radius for circles, bounds for rectangles, even–odd point-in-polygon for freehand).

**Correlations — colocalization.** Pearson (−1…1) over pixels A, B in the region:

```
r = [ nΣAB − ΣA·ΣB ] / √( [nΣA² − (ΣA)²] · [nΣB² − (ΣB)²] )
```

Manders (0…1), each threshold above background:

```
M1 = Σ( A_i · [ B_i > threshold_B ] ) / Σ A_i      (fraction of A overlapping B)
M2 = Σ( B_i · [ A_i > threshold_A ] ) / Σ B_i      (fraction of B overlapping A)
```

**Measure Angles.** Interior angle at the vertex from the clicked coordinates (reflex option reports 360 − θ):

```
θ = atan2( |v1 × v2| , v1 · v2 )      v1 = arm1 − vertex,  v2 = arm2 − vertex
```

**Particle Analyzer.** Threshold (Otsu = max between-class variance of the histogram) → binary mask → connected components. Per object:

```
area          = pixel count
centroid      = mean of pixel-center (x, y)
perimeter     = 4-neighbour edges to background (px)
circularity   = min(1, 4π · area / perimeter²)        (1 = perfect disc)
equiv. diameter = 2·√(area / π)
major / minor axis, aspect ratio  from second central moments (μ20, μ02, μ11)
mean / integrated / min / max intensity  of the raw channel over the object
```

With a **Calibration** (pixel size + unit) every length is in that unit and area in unit².

---

## Disclaimer

This software is provided **"as is", without warranty of any kind**. You use these tools **entirely at
your own risk**. The author accepts **no responsibility or liability** for any error, inaccuracy, data
loss, or damage arising from its use. You are solely responsible for validating any output before using
it for research, publication, or any other purpose.

## Copyright

© 2026 Sanjaya VBD Aththawala Gedara. All rights reserved.

**Anyone is free to use these tools, for any purpose, at no cost.** If the software contributes to
published work, please **cite** it:

> Aththawala Gedara, S. V. B. D. (2026). imgTKT (version 1). https://sanjayaviraj.github.io/imgTKT

All other rights are reserved. You may **not** copy, modify, redistribute, sublicense, or sell this
software without the author's prior written permission.
