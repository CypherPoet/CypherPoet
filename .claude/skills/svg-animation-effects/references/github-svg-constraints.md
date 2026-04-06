# GitHub SVG Sanitization Rules

GitHub processes SVGs through a sanitizer before rendering. Understanding what survives is critical for animated README SVGs.

## Rendering Context

- Markdown `![alt](file.svg)` renders as `<img src="file.svg">`
- `<img>` context means: no scripting, no interaction, no external resources
- Animations auto-play and loop indefinitely
- No hover, focus, click, or other user-triggered states

## Allowed Elements

| Element | Notes |
|---------|-------|
| `<svg>` | Root element, must have xmlns |
| `<path>`, `<circle>`, `<rect>`, `<ellipse>`, `<line>`, `<polygon>`, `<polyline>` | Basic shapes |
| `<g>` | Grouping |
| `<defs>` | Definition container |
| `<use>` | Reference shapes from `<defs>` within same SVG |
| `<linearGradient>`, `<radialGradient>`, `<stop>` | Gradients |
| `<clipPath>` | Clipping |
| `<mask>` | Masking |
| `<filter>`, `<feGaussianBlur>`, `<feOffset>`, `<feColorMatrix>` | Filters (limited) |
| `<text>`, `<tspan>` | Text (uses system fonts only) |
| `<style>` | CSS including `@keyframes` |
| `<animate>`, `<animateTransform>` | SMIL animations (also work) |

## Blocked Elements

| Element | Reason |
|---------|--------|
| `<script>` | Security |
| `<foreignObject>` | Can embed HTML/JS |
| `<iframe>`, `<embed>`, `<object>` | External content |
| `<a>` with `javascript:` | XSS vector |
| Any element with `on*` attributes | Event handlers |

## CSS Properties That Work

- `transform` (translate, scale, rotate, skew)
- `opacity`
- `fill`, `stroke`, `stroke-width`
- `stroke-dasharray`, `stroke-dashoffset`
- `filter: drop-shadow()`
- `visibility`
- `@keyframes` with all above properties
- `animation` shorthand and individual properties
- `transition` (but no trigger, so less useful)

## CSS Properties That May Not Work

- `backdrop-filter`
- Advanced filter chains (browser-dependent)
- `content` (pseudo-elements)
- Font loading (`@font-face` with external URLs)

## External Resources

- **Blocked**: `url()` pointing to external domains, `xlink:href` to external SVGs
- **Allowed**: `url(#id)` referencing `<defs>` within the same SVG
- System fonts work in `<text>` elements: `-apple-system`, `Arial`, `Helvetica`, etc.

## Caching

GitHub caches rendered SVGs through their CDN (`camo`). During development:
- Append `?v=N` to the image URL to bust cache
- Or use a commit hash: `![](./assets/file.svg?raw=true)`
- After pushing, cached versions can take minutes to update

## Sizing

- Always set explicit `width` and `height` on the root `<svg>` element
- `viewBox` alone may not size correctly in markdown context
- Common banner size: `width="800" height="200"`
- Common avatar size: `width="200" height="200"`
- Common divider size: `width="800" height="12"`
