# Font Embedding in SVG

## Base64 Encoding

Convert a font file to a base64 string for inline embedding:

```bash
# macOS
base64 -i MyFont.ttf -o /tmp/font-b64.txt

# Linux
base64 MyFont.ttf > /tmp/font-b64.txt
```

## Format Detection

| Extension | MIME type | `format()` value |
|-----------|----------|-----------------|
| `.ttf`    | `font/ttf` | `truetype` |
| `.otf`    | `font/otf` | `opentype` |
| `.woff2`  | `font/woff2` | `woff2` |
| `.woff`   | `font/woff` | `woff` |

## @font-face Syntax in SVG

```xml
<svg xmlns="http://www.w3.org/2000/svg" width="800" height="120">
  <style>
    @font-face {
      font-family: 'MyFont';
      src: url('data:font/ttf;base64,AAAA...') format('truetype');
    }
  </style>
  <text font-family="'MyFont', sans-serif" font-size="48"
        x="400" y="70" text-anchor="middle" fill="#e6edf3">
    Hello World
  </text>
</svg>
```

Key points:
- The `@font-face` block goes inside `<style>` within the SVG
- The `font-family` name is arbitrary — pick something descriptive
- Always include a fallback (`sans-serif`) in the `font-family` attribute on `<text>`
- Quote the font family name if it contains spaces

## Size Considerations

Base64 encoding adds ~33% overhead to the raw font file:

| Font file | Base64 in SVG | Suitable for |
|-----------|--------------|-------------|
| < 50KB | ~67KB | Any use, small SVG |
| 50-200KB | 67-267KB | Fine for README banners |
| 200-500KB | 267-667KB | Acceptable, consider subsetting |
| > 500KB | > 667KB | Consider font subsetting or path conversion |

### Font Subsetting

If the font is large and you only need specific characters, use a tool like `pyftsubset` (from `fonttools`) to create a subset:

```bash
pip install fonttools brotli
pyftsubset MyFont.ttf --text="Hello World" --output-file=MyFont-subset.ttf
```

This can dramatically reduce file size (e.g., 400KB → 20KB for a short string).

## GitHub Compatibility

- `<style>` blocks with `@font-face` survive GitHub's SVG sanitizer
- `data:` URLs with base64-encoded fonts work (no external URL needed)
- The SVG is rendered via `<img>` tag, so the font must be embedded — external URLs are blocked
- System fonts are available as fallbacks: `-apple-system`, `Arial`, `Helvetica`, `monospace`
- Emoji characters (👋, 🧬, etc.) render using the viewer's system emoji font, not the custom font

## Using with Python

For programmatic SVG generation (recommended when font base64 is large):

```python
import base64

with open("MyFont.ttf", "rb") as f:
    font_b64 = base64.b64encode(f.read()).decode()

svg = f'''<svg xmlns="http://www.w3.org/2000/svg" width="800" height="120"
          viewBox="0 0 800 120">
  <style>
    @font-face {{
      font-family: 'MyFont';
      src: url('data:font/ttf;base64,{font_b64}') format('truetype');
    }}
  </style>
  <text x="400" y="70" fill="#e6edf3"
        font-family="'MyFont', sans-serif" font-size="48"
        text-anchor="middle">Hello World</text>
</svg>'''

with open("assets/text-banner.svg", "w") as f:
    f.write(svg)
```

This avoids shell quoting issues and handles arbitrarily large base64 strings cleanly.
