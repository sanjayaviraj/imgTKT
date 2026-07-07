# imgTKT — A Quick Image Analysis Tool Kit

To access this browser-based image analysis tool, go to [https://github.com/sanjayaviraj/imgTKT ](https://sanjayaviraj.github.io/imgTKT)

Browser-based tools for microscopy and scientific image analysis. Each tool is a single
self-contained HTML file — no build step, no server, no install. Everything runs locally in the
browser, so **images are never uploaded anywhere**. Open `index.html` to reach all tools.

Supported inputs: multi-channel **TIFF / OME-TIFF** (channels detected automatically) and RGB
**PNG / JPG** (split into channels).

**Z-stacks & time series.** A TIFF's pages can be channels, Z-slices, or time points, and the raw page
list alone is ambiguous — so the tools read the **OME-XML** or **ImageJ/Fiji** metadata to tell them
apart. A 2-channel × 15-slice stack loads as **2 channels** (not 30), with a **Z-slice / time-point
slider** in the viewer to scrub through planes; measurements always use the raw pixels of the plane
you're viewing. For plain multi-page TIFFs with no such metadata, a small "these pages are: channels /
Z-slices / time points" control lets you set it yourself.

| Tool | File | Purpose |
|------|------|---------|
| Size Analysis | [`size_analysis.html`](size_analysis.html) | Measure feature size (FWHM) from a line profile via Gaussian fitting |
| Line Profile | [`line_profile.html`](line_profile.html) | Read the intensity profile of every channel along a drawn line |
| ROI Inspector | [`roi_inspector.html`](roi_inspector.html) | Measure mean & sum intensity per channel inside a region |
| Correlations | [`correlations.html`](correlations.html) | Pearson & Manders colocalization between two channels |
| Plot Data | [`plot_data.html`](plot_data.html) | Plot exported CSV/TSV data — scatter, line, bar, histogram, pie |

**Records survive navigation.** ROI Inspector, Correlations, and Size Analysis save their recorded
table (only the rows/measurements — never the image) to the browser's `localStorage`, so moving between
tools or reloading the page keeps your data. When you reopen a tool that has saved rows, a banner asks
whether to **Keep** or **Clear** them; reload the same image to line everything back up. Nothing leaves
the browser.

A key principle across all tools: **displayed appearance and measured values are separate.**
Brightness, lookup tables, normalization, and the threshold preview only change how the image looks —
every reported number and CSV value is computed from the **raw pixel data**.

---

## Size Analysis — feature size by FWHM

**How it works:** load an image, choose a channel, set the pixel size (nm/px). Draw a line across a
feature; the tool samples intensity along the line (averaging over the chosen line width), then fits a
Gaussian to the profile.

**Calculation.** The profile is fit to

```
I(x) = offset + amplitude · exp( −(x − center)² / (2σ²) )
```

by non-linear least squares (Levenberg–Marquardt). The full width at half maximum is

```
FWHM = 2·√(2·ln 2) · σ  ≈  2.3548 · σ
```

reported in nm using the pixel size. Fit quality is reported as R². Results (FWHM, amplitude, center,
σ, offset, R²) can be saved and exported to CSV, with mean ± SD across measurements.

## Line Profile — per-channel intensity along a line

**How it works:** draw a line on a multi-channel image and read the intensity profile of every channel
at once, plotted with per-channel colors.

**Calculation.** The line is sampled in even steps; at each step the value is the mean of the pixels
across the line width (perpendicular to the line). Distance is `step · pixelSize`. An optional
**Normalize** toggle rescales each channel to 0–1 for display only (`(v − min)/(max − min)` per
channel); the CSV export stays in raw intensity units (distance + one column per channel).

## ROI Inspector — mean & sum intensity in a region

**How it works:** place a **freehand**, **circular**, or **rectangular** region (fixed-size drop is
available for circle/rectangle) and read the **mean** and **sum** intensity of every channel
inside it. Both are shown live in the Current ROI box (as `mean C1`, `sum C1`, …) and recorded per measurement.

**Calculation.** For a region containing N pixels, per channel c,

```
sum_c  = Σ (raw intensity of channel c over pixels in the region)
mean_c = sum_c / N
```

Region membership is tested at each pixel center: inside-radius for circles, bounds for rectangles,
and an even–odd point-in-polygon test for freehand outlines. Each measurement (per-channel mean + sum +
pixel count + label) is recorded and exportable to CSV (`mean_C*`, `sum_C*` columns).

## Correlations — colocalization between two channels

**How it works:** pick two channels and a region (**freehand / circle / rectangle / whole image**),
then compute **Pearson** or **Manders** coefficients. A live **threshold preview** can black out
sub-threshold pixels so you can see what each threshold keeps.

**Pearson correlation coefficient** (how much the two channels rise and fall together, −1…1):

```
r = [ nΣAB − ΣA·ΣB ] / √( [nΣA² − (ΣA)²] · [nΣB² − (ΣB)²] )
```

over the pixels A, B in the region.

**Manders colocalization coefficients** (fraction of one channel's signal that overlaps the other,
0…1):

```
M1 = Σ( A_i · [ B_i > threshold_B ] ) / Σ A_i        (fraction of A overlapping B)
M2 = Σ( B_i · [ A_i > threshold_A ] ) / Σ B_i        (fraction of B overlapping A)
```

Each threshold is the intensity cutoff for its own channel; raising it above the background is what
makes Manders meaningful (with a zero threshold, almost every pixel counts as "signal" and both
coefficients trend to 1). Results are recorded and exported to CSV with the channels, region, values,
and thresholds used.

## Plot Data — plot exported CSV / TSV

**How it works:** load (or paste) a CSV/TSV — for example the data exported from any of the tools
above. The first row is treated as column headers, and numeric columns are detected automatically. The
data is shown in a table in the middle; pick columns there (click a header) or from the control panel,
choose a chart type, and the plot updates live.

**Chart types.** *Scatter* (numeric X vs one or more numeric Y), *Line* and *Bar* (any column as the
X category vs numeric Y series), *Histogram* (bin a single numeric column into a chosen number of
equal-width bins and count values per bin), and *Pie / Doughnut* (slice a label column by a value
column, or by counting how often each label occurs). Appearance options cover title, axis labels,
point size, smoothing, area fill, legend, and grid. The figure exports to PNG (on a white background)
and the plotted values export to CSV. The figure also exports to **SVG (vector)** — every line, bar,
slice, and label is an editable object that opens directly in Illustrator / Inkscape (far more useful
than a flat PNG for making publication figures). Everything runs locally — nothing is uploaded.

---

## Running

Open `index.html` in any modern browser (Chrome, Firefox, Edge) — no server needed. The folder can
also be served as a static site (e.g. GitHub Pages).

## Dependencies (loaded from CDNs at runtime)

- [geotiff.js](https://geotiffjs.github.io/) — multi-channel TIFF / OME-TIFF decoding
- [Chart.js](https://www.chartjs.org/) — plots in Size Analysis, Line Profile, and Plot Data
- [UTIF.js](https://github.com/photopea/UTIF.js) — fallback TIFF decoder
- [canvas2svg](https://github.com/gliffy/canvas2svg) — vector SVG export in Plot Data

To run fully offline (and be immune to CDN changes), download these into the repo and point the
`<script src="…">` tags at the local copies.

---

## Copyright

© 2026 Sanjaya VBD Aththawala Gedara. All rights reserved.

This software is **proprietary**. No license is granted to use, copy, modify, or distribute it, in
whole or in part, without the author's prior written permission.
