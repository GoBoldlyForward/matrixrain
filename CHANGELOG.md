# Changelog

## 1.0.0 — 2026

Initial release. The iconic Matrix digital rain as a drop-in canvas
plugin — no framework, no build step, ~5KB of JS.

### Features
- `new MatrixRain(target, options?)` mounts a `<canvas>` into a host
  element (or paints directly into a `<canvas>` you pass).
- Seven built-in themes: `matrix` (default), `binary`, `emoji`,
  `terminal`, `bloodrain`, `arctic`, `gbf`. Each is a preset of
  charset, color, lead color, background, fade alpha, glow, and font
  family.
- Live-tune any option via `setOptions()` — themes swap without a
  reset; per-instance overrides persist across theme swaps until
  cleared with `null`.
- Custom themes via `MatrixRain.THEMES.<name> = { ... }`.
- Lead-head bright glyph (white-tipped column heads) for the iconic
  single-bright-edge effect.
- DPR-aware crisp rendering.
- `ResizeObserver`-driven auto-resize.
- Transparent-background mode via `globalCompositeOperation =
  'destination-out'`, so host gradients, images, or video show
  through the rain.
- Zero-config stacking: anything inside the host that isn't
  `.matrixrain__canvas` auto-promotes above the canvas.
- Methods: `start`, `pause`, `resume`, `stop`, `setOptions`, `resize`,
  `destroy`.

### Package
- Name: `@goboldlyforward/matrixrain` (scoped, `publishConfig.access:
  public`).
- License: MIT.
- Files: `matrixrain.js`, `matrixrain.css`, `README.md`, `LICENSE`,
  `CHANGELOG.md`.
