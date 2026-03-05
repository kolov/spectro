# Spectro — Spectrometer Box Design Tool

## Project overview
Single-file interactive HTML page (`spectrometer.html`) for visualizing transmission diffraction grating spectrometer geometry and computing box dimensions for physical construction.

## Architecture
- **Single file**: All HTML, CSS, JS, and canvas rendering in `spectrometer.html`
- **No build step**: Static HTML served directly via GitHub Pages
- **Deployment**: `.github/workflows/pages.yml` deploys the repo root to GitHub Pages on push to `main`

## Key physics
- **Transmission grating equation**: `sin(β) = sin(α) + m·λ/d`
  - α = incidence angle, β = diffraction angle, m = order, λ = wavelength, d = grating spacing
- Slit and sensor are on **opposite sides** of the grating (transmission, not reflection)
- Sensor plane is perpendicular to the central diffracted ray

## Code structure in spectrometer.html
- **Controls section** (~lines 1-85): Sliders for all parameters
- **`getParams()`**: Reads all slider values
- **`diffractionAngle()`**: Computes diffraction angle for given wavelength
- **`draw()`**: Main canvas rendering — box geometry, light paths, lens, dimensions
- **`wallInsideLens()`** (inside draw): Oriented rectangle clipping for lens-wall intersection
- **`showCutout` click handler** (~line 885+): Generates unfolded box SVG in new tab with dimension arrows

## Box geometry conventions
- Slit sits on the **left box wall** (boxMinX = slitX, no padding on left)
- Grating bottom aligns with box bottom (configurable gap via slider)
- Lens modeled as a cylinder (rectangle in top-down view), barrel auto-sized to protrude ~10mm past box wall
- Cutout SVG uses viewBox in mm units for scalable printing

## Do not commit or push unless explicitly asked.
