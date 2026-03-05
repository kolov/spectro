# Spectro — Spectrometer Box Design Tool

## Project overview
Single-file interactive HTML page (`spectrometer.html`) for visualizing transmission diffraction grating spectrometer geometry and computing box dimensions for physical construction.

## Architecture
- **Single file**: All HTML, CSS, JS, and canvas rendering in `spectrometer.html`
- **No build step**: Static HTML served directly via GitHub Pages
- **Deployment**: `.github/workflows/pages.yml` deploys the repo root to GitHub Pages on push to `main`

## Physical model

### Coordinate system
- Origin at grating center. X = horizontal (right), Y = vertical (up).
- Top-down view: the drawing shows the box from above.

### Light path
1. Light enters **horizontally** from a slit on the left wall of the box.
2. Slit is always at `(-slitDist, 0)` — same height as grating center.
3. Light hits the **transmission diffraction grating** at the origin.
4. The grating can be **tilted** by angle α from vertical. Tilt rotates the grating line from (0,1) toward (+X), so grating direction becomes `(sin α, cos α)`.
5. The grating normal (perpendicular, pointing toward +X side) is `(cos α, -sin α)`.
6. The incidence angle on the grating equals the tilt angle α.

### Diffraction
- **Grating equation**: `sin(β) = sin(α) + m·λ/d` where β is the exit angle from the grating normal, m is the diffraction order, λ is wavelength, d = 1/N (N = lines/mm).
- `diffractionAngle()` returns the **absolute angle from horizontal**: `β_absolute = arcsin(sin α + mλ/d) - α`. This is the direction the diffracted ray travels in the box coordinate system.
- For **0-order** (m=0): `β = α`, so `β_absolute = 0` — the ray continues horizontally.

### Spectrum formation
- The central wavelength `(λ_min + λ_max) / 2` defines the **central diffracted ray** at angle `centralBeta`.
- The **spectrum plane** (where the lens front sits) is perpendicular to the central ray, at distance `sensorDist` from the grating.
- Each wavelength's ray intersects this plane at a different position → the spectrum.

### Camera/lens model
- The lens is a **cylinder** (rectangle in top-down view) with configurable body diameter.
- **Lens front** (glass) is at the spectrum position (`sensorDist` from grating along central ray), facing the grating.
- **Barrel** extends from the lens front **away from the grating** (in the +centralBeta direction), with configurable length.
- **Camera sensor** is at the far end of the barrel (farthest from grating).
- **Aperture**: the effective glass opening, shown as a gap in the front face.

### Field of view (FOV)
- **Optical center** is at `focalLength` distance from the sensor, toward the glass: `sensor_pos - focalLength * (cdx, cdy)`.
- **FOV half-angle** = `atan(halfSensorWidth / focalLength)`.
- **FOV lines**: straight rays from sensor edges through the optical center, extended toward the scene. These show what the camera captures.
- **Captured wavelengths**: rays whose angle offset from the central ray satisfies `|f · tan(β - centralBeta)| ≤ halfSensorWidth`.

### 0-order contamination check
- The 0-order passes straight through the grating — the camera sees the slit directly.
- Check: is the **slit position** within the FOV cone? Computed as the angle from the optical center to the slit, compared against `fovHalfAngle`.
- This matches the visual FOV lines: if the slit is between them, 0-order contaminates.

### Box geometry
- **Left wall**: at `x = slitX` (slit sits on this wall, no padding).
- **Bottom wall**: grating bottom edge defines the baseline (with configurable gap below).
- **Right/Top walls**: padded to fit spectrum endpoints + wall padding + extra length slider.
- **Lens wall cuts**: where the lens barrel intersects box walls, computed via oriented rectangle clipping (`wallInsideLens`). Projects wall segments onto the lens local axes (barrel direction and perpendicular) and clips to barrel bounds.

### Cutout template
- "Show Cutout" opens a new tab with 4 unfolded wall panels in SVG.
- Each panel shows: wall dimensions, slit mark (left wall), grating mark (bottom wall), lens cut rectangles, all with dimension arrows.

## Code structure in spectrometer.html
- **Controls section** (~lines 1-95): Sliders for all parameters
- **`getParams()`**: Reads all slider values
- **`diffractionAngle()`**: Returns absolute exit angle from horizontal
- **`draw()`**: Main canvas rendering — box, light paths, grating, lens, FOV, dimensions
- **`wallInsideLens()`** (inside draw): Oriented rectangle clipping for lens-wall intersection
- **`showCutout` click handler**: Generates unfolded box SVG in new tab with dimension arrows

## Do not commit or push unless explicitly asked.
