# matrixrain

The iconic Matrix digital rain as a drop-in canvas plugin. Ships with classic green katakana plus **binary**, **emoji**, **terminal**, **bloodrain**, **arctic**, and **gbf** themes — and a tiny API for live-tuning font size, speed, and density.

## Demo

[goboldlyforward.github.io/matrixrain](https://goboldlyforward.github.io/matrixrain/) — swap themes, drag the sliders, watch the rain react in real time.

## What it does

Mounts a `<canvas>` inside any container, paints a per-frame trail-fade digital rain over it, and exposes a small live-tuning API. Resizes automatically with its host (via `ResizeObserver`), respects device pixel ratio, and supports transparent backgrounds so you can layer rain over your own gradients, images, or video.

## Install

```bash
npm install @goboldlyforward/matrixrain
```

The script is UMD, so a bundler import works (the CSS ships alongside):

```js
import MatrixRain from '@goboldlyforward/matrixrain';
import '@goboldlyforward/matrixrain/matrixrain.css';
```

Or drop it straight in from a CDN — no build step; the `<script>` exposes the `MatrixRain` global:

```html
<link rel="stylesheet" href="https://unpkg.com/@goboldlyforward/matrixrain/matrixrain.css">
<script src="https://unpkg.com/@goboldlyforward/matrixrain/matrixrain.js"></script>
```

## Usage

```html
<div id="hero" style="height: 60vh"></div>

<script>
  const rain = new MatrixRain('#hero');

  // Live-tune anything, anytime.
  rain.setOptions({
    theme:    'binary',
    fontSize: 18,
    speed:    50,        // ms per frame; lower = faster
    density:  0.7,       // 0–1; fraction of columns actively raining
  });
</script>
```

`new MatrixRain(target, options?)` mounts a canvas into `target` (selector or element) and starts the animation. If `target` is itself a `<canvas>`, it paints directly into it.

## Themes

Seven ship in the box. Each is a preset of `charset · color · leadColor · background · fadeAlpha · glow · fontFamily`.

| Theme       | Vibe                                              |
| ----------- | ------------------------------------------------- |
| `matrix`    | Classic green katakana + digits on black (default) |
| `binary`    | 0/1 in lime on black                              |
| `emoji`     | Weather + cosmic emoji on midnight                |
| `terminal`  | Vintage amber on dark coal                        |
| `bloodrain` | Red on near-black, dense glow                    |
| `arctic`    | Pale cyan on near-white (inverted)               |
| `gbf`       | Yellow binary on black (matches the GBF site)    |

Swap at any time:

```js
rain.setOptions({ theme: 'bloodrain' });
```

Override any field per-instance — your overrides persist across theme swaps until you explicitly clear them with `null`:

```js
// "Matrix charset, but in cyan"
new MatrixRain('#hero', { theme: 'matrix', color: '#22d3ee' });

// Later, fall back to the theme's color:
rain.setOptions({ color: null });
```

Define a new theme by extending `MatrixRain.THEMES`:

```js
MatrixRain.THEMES.synthwave = {
  charset:    '01ABCDEF',
  color:      '#ff2e93',
  leadColor:  '#fff',
  background: '#1a0030',
  fadeAlpha:  0.06,
  glow:       8,
  fontFamily: '"Courier New", monospace',
};

rain.setOptions({ theme: 'synthwave' });
```

## Options

```js
new MatrixRain('#hero', {
  theme:       'matrix',     // 'matrix' | 'binary' | 'emoji' | 'terminal' | 'bloodrain' | 'arctic' | 'gbf'
  charset:     null,         // string or array; overrides theme's glyph set
  color:       null,         // CSS color; overrides theme
  leadColor:   null,         // brighter color for the leading head glyph; set false to disable
  background:  null,         // theme default, or 'transparent' to show the parent through
  fadeAlpha:   null,         // 0–1; trail-fade alpha per frame (lower = longer trails)
  fontSize:    16,           // px
  fontFamily:  null,         // overrides theme
  speed:       33,           // ms per frame
  density:     1.0,          // 0–1; fraction of columns actively raining
  glow:        null,         // px shadowBlur; overrides theme
  columnRatio: null,         // column step = fontSize * columnRatio (theme default 1.0)
  rowRatio:    1.0,          // row step = fontSize * rowRatio
  resetChance: 0.025,        // probability per frame a fallen drop resets to top
  paused:      false,
  autoStart:   true,
});
```

## Methods

```js
rain.start();
rain.pause();
rain.resume();
rain.stop();                 // clears canvas
rain.setOptions({ ... });    // merge + apply (hot-swap any option)
rain.resize();               // manual re-measure (auto via ResizeObserver)
rain.destroy();              // tear down canvas + listeners
```

## Transparent over your own background

```js
new MatrixRain('#hero', { background: 'transparent' });
```

When `background: 'transparent'`, the plugin fades trails via `globalCompositeOperation = 'destination-out'`, so whatever you paint on the host element (CSS gradient, image, video) shows through the rain.

## Layer text & UI above the rain

The CSS hands you a zero-config stacking context — anything inside the host that isn't `.matrixrain__canvas` is auto-promoted above the canvas:

```html
<header class="hero">
  <div id="rain"></div>
  <h1>Welcome to the desert of the real.</h1>
</header>

<style>
  .hero { position: relative; height: 60vh; }
  #rain { position: absolute; inset: 0; }
  .hero h1 { position: relative; z-index: 1; color: #fff; }
</style>

<script> new MatrixRain('#rain'); </script>
```

## How the trail works

Each frame, the plugin paints a translucent `fillRect` over the whole canvas — this is the "fade" that turns yesterday's character into a dim ghost. Then it draws one fresh glyph per column at the current drop position. The brightest pixel is the *head*; everything above it is the same column's character history fading toward background.

When `leadColor` is set, the head is drawn in a brighter color, and the *previous* head (one row up) is re-drawn in the normal color — that downgrade is what gives the iconic single-bright-edge effect without needing per-row history.

## Requirements

HTML, CSS, and ~5KB of JavaScript. No framework, no build step. Uses `<canvas>` + `ResizeObserver`.

## Roadmap

- [x] Canvas-based rain with theme presets
- [x] Lead-head bright glyph (white-tipped column heads)
- [x] DPR-aware crisp rendering
- [x] ResizeObserver-driven auto-resize
- [x] Live `setOptions()` for every field
- [x] Theme swap with no color bleed
- [x] Built-in themes: matrix, binary, emoji, terminal, bloodrain, arctic, gbf
- [x] Transparent-background mode (composite over parent bg)
- [x] Demo with controls + theme gallery
- [x] Publish to npm (as `@goboldlyforward/matrixrain`)
- [ ] Optional `matrixrain-rails` gem wrapper
- [ ] GitHub Actions CI (stylelint + eslint)
- [ ] Deploy demo to gh-pages
- [ ] `requestAnimationFrame`-driven scheduling option (in addition to setInterval-style)
- [ ] Per-column speed jitter for varied fall rates
- [ ] Theme transitions (cross-fade between themes)

## License

MIT — see [LICENSE](LICENSE).
