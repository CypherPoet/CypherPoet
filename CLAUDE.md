# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GitHub profile repository for [CypherPoet](https://github.com/CypherPoet). The `README.md` is displayed on the GitHub profile page and contains stat badges. The `playgrounds/` directory contains an interactive HTML tool for generating GitHub profile READMEs.

## Architecture

### `playgrounds/readme-playground.html`

A self-contained, single-file vanilla JavaScript application (no frameworks, no external dependencies). It provides:

- Split-screen UI: control panel (left) + live markdown preview (right)
- GitHub dark theme via CSS custom properties
- Real-time markdown generation from form inputs (header, about, skills, stats widgets, social links)
- Preset system for loading example profile templates
- Copy-to-clipboard for generated markdown

Key functions: `generateMarkdown()` orchestrates markdown creation, `renderPreview()` renders it with GitHub styling, `updateAll()` ties UI state to output. Social links are managed dynamically via `renderSocialList()`.

## Lessons

- When referencing a GitHub Action, verify the exact published tag (e.g. check the repo's tags/releases) instead of assuming a `@v1` floating tag exists. Not all action maintainers publish floating major-version tags — some only publish exact versions like `v1.0`.

## Development

No build system, package manager, tests, or linting. To preview the playground locally:

```sh
python3 -m http.server 8000 -d playgrounds
# Open http://localhost:8000/readme-playground.html
```
