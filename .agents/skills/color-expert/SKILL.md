---
name: color-expert
description: Use when working with color naming, color theory, color spaces, color definitions, or any task involving color knowledge - palettes, ramps, gradients, conversions, accessibility, perceptual matching, pigment mixing, print-vs-screen color, CSS color syntax, or historical color terminology. Use this skill whenever the user is choosing, comparing, generating, naming, converting, or explaining colors, even if they do not explicitly ask for "color theory." Also use when generating CSS custom properties, design tokens, or when any UI color decision needs to be made — including dark mode, wide-gamut, or accessibility review.
---

# Color Expert

A comprehensive knowledge base for color-related work. See `references/INDEX.md` for 140+ detailed reference files; this skill file contains the essential knowledge to answer most questions directly.

## Color Spaces — What to Use When

| Task                            | Use                                    | Why                                                                       |
| ------------------------------- | -------------------------------------- | ------------------------------------------------------------------------- |
| Perceptual color manipulation   | **OKLCH**                              | Best uniformity for lightness, chroma, hue. Fixes CIELAB's blue problem.  |
| CSS gradients & palettes        | **OKLCH** or `color-mix(in oklab)`     | No mid-gradient darkening like RGB/HSL                                    |
| Gamut-aware color picking       | **OKHSL / OKHSV**                      | Ottosson's picker spaces — cylindrical like HSL but perceptually grounded |
| Normalized saturation (0-100%)  | **HSLuv**                              | CIELUV chroma normalized per hue/lightness. HPLuv for pastels.            |
| CSS-native whiteness/blackness  | **HWB**                                | Intuitively adjust tint/shade; fully sRGB; now in CSS Color 4             |
| Print workflows                 | **CIELAB D50**                         | ICC standard illuminant                                                   |
| Screen workflows                | **CIELAB D65** or OKLAB                | D65 = screen standard                                                     |
| Cross-media appearance matching | **CAM16 / CIECAM02**                   | Accounts for surround, adaptation, luminance, and viewing conditions      |
| HDR                             | **Jzazbz / ICtCp**                     | Designed for extended dynamic range                                       |
| Pigment/paint mixing simulation | **Kubelka-Munk** (Spectral.js, Mixbox) | Spectral reflectance mixing, not RGB averaging                            |
| Color difference (precision)    | **CIEDE2000**                          | Gold standard perceptual distance                                         |
| Color difference (fast)         | **Euclidean in OKLAB**                 | Good enough for most applications                                         |
| Video/image compression         | **YCbCr**                              | Luma+chroma separation enables chroma subsampling                         |

### Understanding HSL's Limitations

HSL isn't "bad" — it's a simple, fast geometric rearrangement of RGB into a cylinder. It's fine for quick color picking and basic UI work. But its three channels don't correspond to human perception:

- **Lightness (L):** fully saturated yellow (`hsl(60,100%,50%)`) and fully saturated blue (`hsl(240,100%,50%)`) have the same L=50% but vastly different perceived brightness. L is a mathematical average, not a perceptual measurement.
- **Hue (H):** non-uniform spacing. A 20° shift near red produces a dramatic change; the same 20° near green is barely visible. The green region is compressed, reds are stretched.
- **Saturation (S):** doesn't correlate with perceived saturation. A color can have S=100% and still look muted (e.g., dark saturated blue).

**When HSL is fine:** simple color pickers, quick CSS tweaks, situations where perceptual accuracy doesn't matter.

**When to use something better:**
- Generating palettes or scales → **OKLCH** (uniform lightness across hues)
- Creating gradients → **OKLAB** or `color-mix(in oklab)` (no mid-gradient darkening)
- Gamut-aware picking with HSL-like UX → **OKHSL** (Ottosson's perceptual HSL)
- Normalized saturation 0–100% → **HSLuv** (CIELUV-based, no out-of-bounds)

### Named Hue (HSL/HSV) Ranges

| Name       | Degrees       |
| ---------- | ------------- |
| **red**    | 345–360, 0–15 |
| **orange** | 15–45         |
| **yellow** | 45–70         |
| **green**  | 70–165        |
| **cyan**   | 165–195       |
| **blue**   | 195–260       |
| **purple** | 260–310       |
| **pink**   | 310–345       |
| **warm**   | 0–70          |
| **cool**   | 165–310       |

### Key Distinctions

- **Chroma** = colorfulness relative to a same-lightness neutral reference
- **Saturation** = perceived colorfulness relative to the color's own brightness
- **Lightness** = perceived reflectance relative to a similarly lit white
- **Brightness** = perceived intensity of light coming from a stimulus
- Same chroma ≠ same saturation. These are different dimensions.

## CSS Color Level 4 & 5 — Practical Syntax

Browser support note: `oklch()`, `oklab()`, `lch()`, `lab()`, `color(display-p3 ...)`, and `hwb()` are all **Baseline 2023** — broadly available. Relative Color Syntax is **Baseline 2024**.

### Modern Color Functions

```css
/* Perceptual spaces — prefer these */
oklch(0.65 0.18 250)          /* lightness chroma hue */
oklab(0.65 -0.05 -0.12)       /* lightness a b */
lch(65 35 250)                 /* CIELAB cylindrical */
lab(65 -10 -25)                /* CIELAB rectangular */

/* Wide-gamut display */
color(display-p3 0.2 0.5 0.9)
color(srgb 0.2 0.5 0.9)        /* explicit sRGB */

/* sRGB-only, CSS Color 4 */
hwb(250 10% 20%)               /* hue whiteness blackness */
```

### `color-mix()` — Beats HSL Mixing

In HSL, mixing `red` and `green` produces a muddy brown because the midpoint passes through the desaturated center. In OKLCH the path stays vivid:

```css
/* Vivid, perceptually even result */
color: color-mix(in oklch, oklch(0.6 0.22 30), oklch(0.6 0.22 145));

/* Tint a brand color with 20% white */
color: color-mix(in oklch, var(--brand) 80%, white);
```

### Relative Color Syntax (CSS Color 5)

Derive hover/active/focus states from a base token without JavaScript:

```css
:root { --brand: oklch(0.55 0.2 260); }

.button:hover {
  /* Lighten by 0.1 lightness units, keep chroma and hue */
  background: oklch(from var(--brand) calc(l + 0.1) c h);
}
.button:active {
  background: oklch(from var(--brand) calc(l - 0.08) c h);
}
/* Desaturate to 50% chroma for a disabled state */
.button:disabled {
  background: oklch(from var(--brand) l calc(c * 0.5) h);
}
```

### Wide-Gamut with Graceful Degradation

```css
/* Feature detection for OKLCH support */
@supports (color: oklch(0 0 0)) {
  :root { --accent: oklch(0.65 0.28 145); }
}

/* P3-enhanced colors for capable displays */
@media (color-gamut: p3) {
  :root { --accent: color(display-p3 0.1 0.85 0.3); }
}

/* sRGB fallback (always defined first) */
:root { --accent: #1db954; }
```

## Implementation Guidance — Code and CSS

When using colors in a program or CSS, add a semantic layer between raw color values and UI roles.

Raw color literals should usually appear only in palette/reference definitions, conversions, diagnostics, or deliberately one-off examples.

- **Use reference tokens for concrete colors**: `ref.red = #f00`
- **Use semantic tokens for meaning/role**: `semantic.warning = ref.red`
- **Prefer semantic tokens in components** so themes can swap meaning without rewriting component code.
- **Encode color decisions when possible** instead of freezing one manual choice into a literal.

Pseudocode examples:
- `ref.red := closest('red', generatedPalette)`
- `semantic.warning := ref.red`
- `semantic.onSurface := mostReadableOn(surface)`

Good pattern: palette/reference tokens define available colors; semantic tokens map those colors to roles like surface, text, accent, success, warning, and danger.

If a system can derive a decision from constraints, encode that derivation. Examples: nearest named hue in a generated palette, foreground chosen by APCA/WCAG target, hover state computed from the base token in OKLCH instead of hand-picking a second unrelated hex.

For larger systems, prefer a **token graph** over a flat token dump: references, semantic roles, derived functions, and scope inheritance.

## Dark Mode — Color Strategy

**Never use "just invert"** — luminance inversion scrambles perceptual relationships. Images look wrong, saturated colors blow out, carefully tuned contrast ratios become invalid.

### Recommended Strategy

Maintain chroma, shift lightness in OKLCH:
- Dark backgrounds: **L 0.10–0.20**
- Light foreground text on dark: **L 0.85–0.95**
- Keep the same hue and chroma — the color identity survives across modes

### Minimal CSS Custom Property Pattern

```css
:root {
  --surface: oklch(0.98 0.01 250);
  --text:    oklch(0.15 0.01 250);
  --accent:  oklch(0.55 0.20 260);
}

@media (prefers-color-scheme: dark) {
  :root {
    --surface: oklch(0.15 0.01 250);
    --text:    oklch(0.93 0.01 250);
    --accent:  oklch(0.72 0.20 260); /* lighter in dark mode to hold contrast */
  }
}
```

### Dark Mode Tips

- Verify **WCAG/APCA contrast for both modes independently** — a pair that passes in light mode can fail in dark.
- **Shadows in dark mode:** use low-opacity colored shadows, not black. Black shadows become invisible or jarring against dark surfaces.
- **Elevation in dark mode:** lighter surface = higher elevation (opposite of light mode drop-shadow convention).
- Avoid pure `#000000` black or `#ffffff` white for surfaces and text — they create harsh contrast and feel uncalibrated. Slight chroma (0.005–0.02) in OKLCH makes neutrals feel intentional.

## Accessibility — Key Numbers

Of ~281 trillion hex color pairs (research by @mrmrs_, computed via a Rust brute-force run):

| Threshold                 | % passing | Odds            |
| ------------------------- | --------- | --------------- |
| WCAG 3:1 (large text)     | 26.49%    | ~1 in 4         |
| WCAG 4.5:1 (AA body text) | 11.98%    | ~1 in 8         |
| WCAG 7:1 (AAA)            | 3.64%     | ~1 in 27        |
| APCA 60                   | 7.33%     | ~1 in 14        |
| APCA 75 (fluent reading)  | 1.57%     | ~1 in 64        |
| APCA 90 (preferred body)  | **0.08%** | **~1 in 1,250** |

APCA is far more restrictive than WCAG at comparable readability. At APCA 90, only 239 billion of 281 trillion pairs work.

## Color Harmony — What Actually Works

### Hue-first harmony is a weak standalone heuristic

Complementary, triadic, tetradic intervals are weak predictors of mood, legibility, or accessibility on their own.

### Character-first harmony works (Ellen Divers' research)

Organize by character (pale/muted/deep/vivid/dark), not hue. **Hue is usually a weaker predictor of emotional response than chroma and lightness** — a muted palette often reads as calm across many hues.

### Legibility = lightness variation

Same lightness regardless of hue is usually illegible. Same character + varied lightness is often more readable.

### The 60-30-10 rule

60% dominant color, 30% secondary, 10% accent.

## Common Pitfalls

### Gradient Dead Zones — How to Fix

**Problem:** gradients in RGB or HSL pass through a desaturated gray zone when the hue arc crosses a "gray corridor." Green → red in HSL travels through yellow and brown; in RGB it collapses into muddy mid-tones.

**Solution:** interpolate in OKLCH or OKLAB:

```css
/* CSS Color 4 hue interpolation — stays vivid */
background: linear-gradient(in oklch, oklch(0.7 0.2 140), oklch(0.7 0.2 30));

/* color-mix equivalent */
background: linear-gradient(
  to right,
  oklch(0.7 0.2 140),
  color-mix(in oklch, oklch(0.7 0.2 140), oklch(0.7 0.2 30)),
  oklch(0.7 0.2 30)
);
```

**Hue arc direction:** by default OKLCH takes the shortest arc. Force longer arc to go "the long way around" (e.g., through cyan instead of through yellow):

```css
background: linear-gradient(in oklch longer hue, oklch(0.7 0.2 140), oklch(0.7 0.2 30));
```

**The yellow dead zone:** OKLCH chroma collapses near hue 100–115° — this is where yellow transitions to green and the sRGB gamut is narrow. Gradients passing through that region may dip visually. Solutions:
- Avoid routing gradients through 100–115° when chroma matters
- Reduce chroma through that region explicitly with extra color stops
- Use `color(display-p3 ...)` if the target display supports P3

### Color Vision Deficiency (CVD) — Practical Checklist

**Prevalence:** ~8% of men, ~0.5% of women have some form of CVD (predominantly red-green: protanopia / deuteranopia). Tritanopia (blue-yellow) is rare (~0.003%) but worth testing for medical, financial, or alert-heavy UIs.

**Design rules:**
- **Never rely on hue alone** to convey information (WCAG Success Criterion 1.4.1)
- Quick self-check: **desaturate your design** — it must remain legible and meaningful in grayscale
- Don't just add icons as the sole redundant cue — some CVD types also struggle with shape-only indicators. Combine: color + text label, color + pattern, color + icon + label

**Simulation in code:**

```js
import { filterDeficiency } from 'culori';
const simulate = filterDeficiency('deuteranopia');
const adjusted = simulate({ mode: 'rgb', r: 0.8, g: 0.2, b: 0.1 });
```

**Tools:**
- **Culori** — `filterDeficiency('protanopia' | 'deuteranopia' | 'tritanopia')`
- **@texel/color** — includes CVD filters, suitable for real-time simulation
- **Chromatic Vision Simulator** — browser extension for full-page preview
- **Color Buddy** — palette-level CVD linting (38 rules including fairness checks)
- **View Color** — per-color CVD preview alongside WCAG/APCA readouts

## Pigment Mixing — Not What You Think

- **Pigment mixing is not well described by the simple subtractive model alone**
- **CMY mixing paths curve outward** (retain chroma = vivid secondaries)
- **RGB mixing paths curve inward** (lose chroma = dull browns)
- **Mixing is non-linear**: proportion of paint ≠ proportional hue change
- **Blue→yellow is a LONG road**, red→yellow is SHORT
- **Tinting strength varies**: blues are concentrated/strong, yellows are weak
- **White doesn't just lighten** — it shifts hue AND kills chroma
- **For spectral/K-M mixing in code**: use Spectral.js (open source) or Mixbox (commercial)

## Color Temperature

- **Temperature ≠ hue** — it's a systematic shift of BOTH hue AND saturation
- **Cool daylight**: blue atmospheric scatter fills shadows
- **Warm incandescent**: favors long wavelengths including infrared
- **Green and purple** do not map cleanly to warm/cool

## Color Naming — Multiple Systems

| System                | Register                   | Example                            |
| --------------------- | -------------------------- | ---------------------------------- |
| ISCC-NBS              | Scientific precision       | "vivid yellowish green"            |
| Munsell               | Systematic notation        | "5GY 7/10"                         |
| XKCD                  | Common perception          | "ugly yellow", "hospital green"    |
| Traditional Japanese  | Cultural/poetic            | "wasurenagusa-iro"                 |
| RAL                   | Industrial reproducibility | RAL 5002                           |
| Ridgway (1912)        | Ornithological             | 1,115 named colors, public domain  |
| CSS Named Colors      | Web standard               | 147 named colors                   |
| color-description lib | Emotional adjectives       | "pale, delicate, glistening"       |

Use `color-name-lists` npm package for 18 naming systems in one import.

## Historical Corrections

- **Moses Harris (1769)** was first to place RYB at equal 120° — his own wheel needed a 4th pigment.
- **Von Bezold (1874)** killed "indigo" as a spectral color
- **The word "magenta"** wasn't used for the subtractive primary until 1907
- **Amy Sawyer (1911)** patented a CMY wheel decades before it became mainstream
- **Elizabeth Lewis (1931)** married trichromatic + opponent process, anticipating CIE Lab by 30 years

## Recommended Tools

### Palette Generation
- **RampenSau** — hue cycling + easing, color space agnostic
- **Poline** — anchor points + per-axis position functions
- **pro-color-harmonies** — adaptive OKLCH harmony, muddy-zone avoidance
- **dittoTones** — extract Tailwind/Radix "perceptual DNA", apply to your hue
- **FarbVelo** — random palettes with dark→light structure
- **IQ Cosine Formula** — `color(t) = a + b*cos(2π(c*t+d))`, 12 floats = infinite palette

### Palette Analysis & Linting
- **Color Buddy** — 38 lint rules (WCAG, CVD, distinctness, fairness, affect)
- **Censor** — Rust CLI, CAM16UCS analysis, 20+ viz widgets
- **Color Palette Shader** — WebGL2 Voronoi, 30+ color models, 11 distance metrics
- **PickyPalette** — interactive sculpting on color space canvas

### Color Libraries (code)
- **Culori** — 30 spaces, 10 distance metrics, gamut mapping, CVD sim
- **@texel/color** — 5–125× faster than Color.js, minimal, for real-time
- **Spectral.js** — open-source K-M pigment mixing
- **RYBitten** — RGB↔RYB with 26 historical color cubes
- **colorgram** — 1 kB image palette extraction
- **random-display-p3-color** — generate random Display P3 colors, zero deps, ESM

### Key Online Tools
- **oklch.com** — OKLCH picker with gamut visualization
- **oklch.com/palette** — OKLCH-based palette generator by Andrey Sitnik
- **Huetone** — accessible color system builder (LCH/OKLCH), by Ardov
- **Ardov Color Lab** — gamut mapping playground, P3 space explorer, harmony generator
- **Components.ai Color Scale** — parametric scale generator
- **Realtime Colors** (realtimecolors.com) — live preview of a full color system on a real UI
- **ColorBox** by Lyft Design — step curve editor for design system scales
- **Accessible Palette** (accessiblepalette.com) — build scales with guaranteed WCAG compliance
- **View Color** — real-time analysis, WCAG + APCA, CVD preview
- **APCA Calculator** — apcacontrast.com
- **Poolors** — unused color combinations finder
