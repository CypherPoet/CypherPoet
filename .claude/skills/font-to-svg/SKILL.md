# font-to-svg

Embeds a custom font into an SVG `<text>` element, producing a self-contained SVG file that renders text in any TTF, OTF, or WOFF2 font — no external dependencies.

## Trigger

When the user wants to render text in a custom font as SVG, embed a font into SVG, or says "/font-to-svg".

## Input

- **Font file path** — path to a `.ttf`, `.otf`, or `.woff2` file
- **Text string** — the text to render
- **Optional config** — font size, fill color, canvas dimensions, text position, background

## Workflow

### 1. Read and Encode the Font

```bash
base64 -i <font-file> -o /tmp/font-b64.txt
```

Detect the format from the file extension:

| Extension | Format string |
|-----------|--------------|
| `.ttf`    | `truetype`   |
| `.otf`    | `opentype`   |
| `.woff2`  | `woff2`      |
| `.woff`   | `woff`       |

Note the file size — base64 encoding adds ~33% overhead. A 400KB font becomes ~530KB in the SVG. Consider whether this is acceptable for the use case.

### 2. Build the SVG

Use Python to assemble the SVG since the base64 string is too large to paste manually:

```python
import base64

with open("<font-file>", "rb") as f:
    font_b64 = base64.b64encode(f.read()).decode()

svg = f'''<svg xmlns="http://www.w3.org/2000/svg" width="{width}" height="{height}" viewBox="0 0 {width} {height}">
  <style>
    @font-face {{
      font-family: '{font_name}';
      src: url('data:font/{format};base64,{font_b64}') format('{format_string}');
    }}
  </style>
  <text x="{width//2}" y="{height//2 + font_size//3}"
        fill="{fill_color}"
        font-family="'{font_name}', sans-serif"
        font-size="{font_size}"
        text-anchor="middle">{text}</text>
</svg>'''

with open("assets/<output-name>.svg", "w") as f:
    f.write(svg)
```

### 3. Defaults

| Property | Default |
|----------|---------|
| Canvas width | `800` |
| Canvas height | `120` |
| Font size | `48` |
| Fill color | `#e6edf3` (light text for dark backgrounds) |
| Background | None (transparent) |
| Text anchor | `middle` (centered) |
| Text position | Centered in canvas |

### 4. Preview and Output

- Open the SVG in browser: `open assets/<name>.svg`
- Provide the markdown embed: `![alt text](./assets/<name>.svg)`

## Notes

- The SVG is fully self-contained — the font is embedded inline, no external URLs
- GitHub's SVG sanitizer allows `<style>` blocks with `@font-face`, so this works in README files
- For styling/animation (gradients, color cycling, glow), compose with the `/svg-animation-effects` skill
- See `svg-animation-effects/references/github-svg-constraints.md` for GitHub SVG sanitization rules
- Emoji characters in the text string will render using the system's emoji font, not the custom font
