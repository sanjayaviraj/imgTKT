# Image Analysis Tools

A small collection of **browser-based tools for microscopy and scientific image analysis**.
Each tool is a single self-contained HTML file — no build step, no server, no install.
Everything runs locally in your browser, so **images are never uploaded anywhere**.

## Tools

| Tool | File | Description |
|------|------|-------------|
| **Size Analysis** | [`size_analysis.html`](size_analysis.html) | Draw a line across a feature, extract its intensity profile, and fit a Gaussian to measure **FWHM**. Loads **multi-channel** TIFF / OME-TIFF (and RGB PNG/JPG) with a channel selector, adjustable LUTs, calibrated pixel size, and per-channel CSV export. |
| **Line Profile** | [`line_profile.html`](line_profile.html) | Draw a line across a **multi-channel** image and read the intensity profile of every channel at once. Clean grid-free plot with per-channel colors and one-click CSV export of the profile data. |
| **ROI Inspector** | [`roi_inspector.html`](roi_inspector.html) | Load multi-channel TIFF / OME-TIFF stacks with automatic channel detection. Place circular and rectangular ROIs to measure mean intensity per channel, then export readings as CSV. |



## Usage

### Online (GitHub Pages)
Once published, visit:

```
https://sanjayaviraj.github.io/imgTKT
```

### Locally
Just open `index.html` in any modern browser (Chrome, Firefox, Edge). No server required.


## Dependencies

These are loaded from CDNs at runtime (an internet connection is needed the first time):

- [Chart.js](https://www.chartjs.org/) — profile plotting (Size Analysis)
- [geotiff.js](https://geotiffjs.github.io/) — multi-channel TIFF decoding (Size Analysis, ROI Inspector)
- [UTIF.js](https://github.com/photopea/UTIF.js) — fallback TIFF decoding (Size Analysis)



## License

[MIT](LICENSE)
