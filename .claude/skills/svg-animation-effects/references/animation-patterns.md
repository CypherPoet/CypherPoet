# CSS Animation Patterns for SVG

Reusable `@keyframes` snippets for each animation type. Adapt colors, durations, and values to the specific SVG.

## Gradient Shift

Animate `stop-color` on gradient stops for a flowing color effect.

```xml
<defs>
  <linearGradient id="grad" x1="0%" y1="0%" x2="100%" y2="0%">
    <stop id="s1" offset="0%" stop-color="#58a6ff"/>
    <stop id="s2" offset="100%" stop-color="#bc8cff"/>
  </linearGradient>
</defs>
<style>
  @keyframes gradShift1 {
    0%   { stop-color: #58a6ff; }
    33%  { stop-color: #bc8cff; }
    66%  { stop-color: #f778ba; }
    100% { stop-color: #58a6ff; }
  }
  @keyframes gradShift2 {
    0%   { stop-color: #bc8cff; }
    33%  { stop-color: #f778ba; }
    66%  { stop-color: #58a6ff; }
    100% { stop-color: #bc8cff; }
  }
  #s1 { animation: gradShift1 8s ease infinite; }
  #s2 { animation: gradShift2 8s ease infinite; }
</style>
<!-- Apply: fill="url(#grad)" -->
```

## Stroke Draw (Line Draw)

Animate `stroke-dashoffset` from path length to 0. The path must have `fill: none`.

```xml
<style>
  @keyframes draw {
    0%   { stroke-dashoffset: 5000; } /* approximate path length */
    100% { stroke-dashoffset: 0; }
  }
  .draw-path {
    fill: none;
    stroke: #58a6ff;
    stroke-width: 2;
    stroke-dasharray: 5000;
    animation: draw 4s ease forwards;
  }
</style>
```

**Tip:** To find the path length, open the SVG in a browser console and run:
```js
document.querySelector('path').getTotalLength()
```

For looping draw + fade:
```xml
@keyframes drawLoop {
  0%   { stroke-dashoffset: 5000; opacity: 1; }
  40%  { stroke-dashoffset: 0; opacity: 1; }
  60%  { stroke-dashoffset: 0; opacity: 1; }
  100% { stroke-dashoffset: 5000; opacity: 0.3; }
}
```

## Color Cycle

Directly animate the `fill` property.

```xml
<style>
  @keyframes colorCycle {
    0%   { fill: #58a6ff; }
    25%  { fill: #bc8cff; }
    50%  { fill: #f778ba; }
    75%  { fill: #ff9a3c; }
    100% { fill: #58a6ff; }
  }
  .cycling { animation: colorCycle 10s ease infinite; }
</style>
```

## Glow Pulse

Pulsing rings behind the shape + drop-shadow on the shape itself.

```xml
<style>
  @keyframes ringPulse {
    0%, 100% { r: 60; opacity: 0; }
    50%      { r: 80; opacity: 0.15; }
  }
  @keyframes glowShadow {
    0%, 100% { filter: drop-shadow(0 0 4px rgba(88,166,255,0.3)); }
    50%      { filter: drop-shadow(0 0 14px rgba(188,140,255,0.6)); }
  }
  .ring { animation: ringPulse 4s ease-in-out infinite; }
  .logo { animation: glowShadow 4s ease-in-out infinite; }
</style>
<circle class="ring" cx="100" cy="100" r="60" fill="none" stroke="#58a6ff" stroke-width="1"/>
```

Stagger multiple rings with `animation-delay`:
```css
.ring1 { animation: ringPulse 4s ease-in-out infinite; }
.ring2 { animation: ringPulse 4s ease-in-out infinite 1s; }
.ring3 { animation: ringPulse 4s ease-in-out infinite 2s; }
```

## Float / Hover

Subtle vertical oscillation.

```xml
<style>
  @keyframes float {
    0%, 100% { transform: translateY(0); }
    50%      { transform: translateY(-5px); }
  }
  .floating { animation: float 3.5s ease-in-out infinite; }
</style>
```

Combine with slight rotation for more organic feel:
```css
@keyframes floatRotate {
  0%, 100% { transform: translateY(0) rotate(0deg); }
  50%      { transform: translateY(-5px) rotate(1deg); }
}
```

## Wave Motion (for dividers)

Horizontal scroll with `translateX` and a `clipPath` to mask the overflow.

```xml
<style>
  @keyframes waveScroll {
    0%   { transform: translateX(0); }
    100% { transform: translateX(-400px); }
  }
  .wave { animation: waveScroll 8s linear infinite; }
</style>
<clipPath id="clip"><rect width="800" height="12"/></clipPath>
<g clip-path="url(#clip)">
  <g class="wave">
    <!-- Draw a wave path wider than the viewport, tiling seamlessly -->
    <path d="M0,6 Q50,0 100,6 Q150,12 200,6 ... Q1150,12 1200,6"
          fill="none" stroke="url(#grad)" stroke-width="2.5"/>
  </g>
</g>
```

The wave path should be 1.5-2x the SVG width so the scroll loops seamlessly.

## Faux-Boids (Swarm)

Each element gets a unique `@keyframes` with hand-designed waypoints. Key principles:

- **Cohesion**: Waypoints cluster near the center of the canvas
- **Separation**: No two elements share exact positions at any keyframe
- **Direction flips**: Use `scaleX(-1)` when the element reverses horizontal direction
- **Varied timing**: Different `animation-duration` per element (16-23s range)
- **Staggered start**: Different `animation-delay` per element (0-3s)
- **Flutter**: Secondary animation for subtle opacity oscillation

```xml
<style>
  @keyframes flock1 {
    0%   { transform: translate(50px, 150px) scale(0.02) scaleX(1) rotate(-5deg); }
    25%  { transform: translate(350px, 80px) scale(0.02) scaleX(-1) rotate(5deg); }
    50%  { transform: translate(620px, 170px) scale(0.02) scaleX(1) rotate(-8deg); }
    75%  { transform: translate(400px, 100px) scale(0.02) scaleX(-1) rotate(3deg); }
    100% { transform: translate(50px, 150px) scale(0.02) scaleX(1) rotate(-5deg); }
  }
  @keyframes flutter {
    0%, 100% { opacity: 0.7; }
    50%      { opacity: 1; }
  }
  .b1 {
    animation: flock1 18s cubic-bezier(0.4,0,0.6,1) infinite,
               flutter 0.8s ease infinite;
  }
</style>
<defs>
  <path id="shape" d="..."/>
</defs>
<use class="b1" href="#shape" fill="url(#grad)" opacity="0.85"/>
```

**Template for 10 elements**: Create `flock1` through `flock10` with:
- 6-9 keyframe stops each (more stops = smoother paths)
- X range: 30-740px, Y range: 55-200px (for an 800x300 canvas)
- Scale: 0.013-0.022 (varied sizes for depth)
- Duration: 16-23s per element
- Delay: 0-3s staggered

## Combining Animations

Multiple animations can be combined on one element:
```css
.element {
  animation: float 3s ease infinite,
             colorCycle 10s ease infinite,
             glowShadow 4s ease infinite;
}
```

Order matters — later animations' properties override earlier ones if they target the same property.
