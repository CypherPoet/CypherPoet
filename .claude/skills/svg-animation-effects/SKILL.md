# svg-animation-effects

Takes a user's SVG file and generates multiple animated SVG variations for use in GitHub profile READMEs and other markdown contexts.

## Trigger

When the user wants to animate an SVG, create animated README graphics, or says "/svg-animation-effects".

## Input

User provides a path to an SVG file (logo, icon, or graphic).

## Workflow

### 1. Analyze the SVG

- Read the file, parse its structure (paths, shapes, groups)
- Note dimensions (`width`, `height`, `viewBox`)
- Extract the color palette from `fill`, `stroke`, and gradient `stop-color` values
- Identify whether it's a single path, multiple shapes, or complex groups
- Estimate the path length for stroke-based animations (if a single path, compute or approximate `getTotalLength()`)

### 2. Generate Animated Variations

Create each as a standalone SVG saved to `assets/` in the user's project. Always set explicit `width` and `height` attributes (not just `viewBox`) for proper markdown rendering.

#### Gradient Pulse
Animated `linearGradient` fill cycling through colors derived from the original palette + complementary hues. Use `@keyframes` to animate `stop-color` on gradient stops.

#### Line Draw
`stroke-dasharray` / `stroke-dashoffset` animation that traces the shape outline over 3-5 seconds. Set the path fill to `none`, stroke to a visible color, and animate dashoffset from the total path length to 0.

#### Color Cycle
Direct `fill` color animation through a harmonious palette. Slower (8-12s cycle) for a subtle, ambient effect.

#### Glow Pulse
Concentric `<circle>` rings with animated `r` (radius) and `opacity`, creating a pulsing radar/sonar effect behind the logo. Combine with a `drop-shadow` filter animation on the logo itself.

#### Float / Hover
Subtle vertical `translateY` oscillation (4-6px amplitude, 3-4s cycle). Simple but effective as a profile avatar.

#### Banner Composition
Full-width banner (800x200) with:
- The logo scaled and positioned on the left
- Text (username / tagline) on the right
- Animated gradient on the text and/or logo
- Dark background (`#0d1117`) for GitHub dark mode

#### Wave Divider
Thin strip (800x12) with an animated sine-wave path that scrolls horizontally via `translateX`. The wave stroke uses an animated gradient matching the logo palette. Useful as a section separator.

#### Boids / Swarm
8-12 small copies of the shape via `<use href="#shape">`, each on a unique CSS `@keyframes` path with:
- Different durations (16-23s) and delays (0-3s)
- `scaleX(-1)` flips when changing direction
- Subtle rotation changes to sell the movement
- A `flutter` opacity animation for liveliness
- Paths designed so shapes loosely cluster mid-canvas (faux cohesion)

### 3. Preview

Open each generated SVG in the browser: `open assets/<name>.svg`

### 4. Output

Print README-ready markdown for each variation:
```
![Gradient Pulse](./assets/logo-gradient.svg)
![Line Draw](./assets/logo-draw.svg)
...
```

## Constraints

See `references/github-svg-constraints.md` for full details.

**Key rules:**
- SVGs must be self-contained — inline `<style>` with `@keyframes`, no external deps
- No `<script>`, no `<foreignObject>`, no event handlers
- GitHub renders SVGs via `<img>`, so no hover/focus/interactive states
- Animations auto-play and loop — no user trigger needed
- Always include explicit `width` and `height` attributes
- GitHub caches SVG aggressively — use query params (`?v=2`) during development to bust cache
- `<use href="#id">` works for referencing shapes defined in `<defs>` within the same SVG
- CSS `transform`, `opacity`, `fill`, `stroke`, `stroke-dasharray`, `stroke-dashoffset`, and `filter: drop-shadow()` all work
