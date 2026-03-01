# Copilot Instructions for Spotify-Readme

Use these instructions as the primary reference. Only search the codebase if information here is incomplete or appears incorrect.

## Project Overview

Spotify-Readme is a Python Flask web application that generates a dynamic SVG widget showing what you're currently listening to (or recently listened to) on Spotify. The widget is designed to be embedded in GitHub README files. It is deployed on Vercel.

## Repository Layout

```
.github/copilot-instructions.md   # This file
.gitignore
README.md
spotify.svg                       # Example rendered widget
api/
  index.py                        # Main Flask app (routing, Spotify API calls, SVG rendering)
  requirements.txt                # Python dependencies
  templates/
    index.html                    # Jinja2 SVG template (output is SVG, not HTML)
  base64/
    placeholder_image.txt         # Base64-encoded fallback album art
    placeholder_scan_code.txt     # Base64-encoded fallback scan code image
    spotify_logo.txt              # Base64-encoded Spotify logo
```

There are **no GitHub Actions workflows** and **no CI pipeline** in this repository (as of this writing).

## Bootstrap & Run

```bash
# Install dependencies (Python 3, pip required)
pip install -r api/requirements.txt

# Run the development server (http://localhost:5000)
python api/index.py
```

Both commands have been validated and work correctly. Flask runs in debug mode when executed directly.

## Lint & Test

There is **no configured linter or test suite**. To check syntax:

```bash
python3 -m py_compile api/index.py
```

When adding features, manually verify by running the app locally and inspecting the rendered SVG in a browser.

## Environment Variables

Required (store in a `.env` file at the repo root, which is gitignored):

- `CLIENT_ID` – Spotify app client ID
- `CLIENT_SECRET` – Spotify app client secret
- `REFRESH_TOKEN` – Spotify OAuth refresh token

## Architecture

- **`api/index.py`** – Flask app with a single catch-all route. Calls Spotify's `me/player/currently-playing` endpoint (falls back to `me/player/recently-played`), fetches album art, generates animated equalizer bars, and renders the SVG template.
- **`api/templates/index.html`** – Jinja2 template that produces an SVG with a `<foreignObject>` wrapping an HTML/CSS layout. Despite the `.html` extension, the output MIME type is `image/svg+xml`.
- **`api/base64/`** – Plain-text files containing base64-encoded fallback images read at startup.

## Widget Query Parameters

The single endpoint (`/`) accepts:

| Parameter | Default | Values          |
|-----------|---------|-----------------|
| `spin`    | `false` | `false`, `true` |
| `scan`    | `false` | `false`, `true` |
| `theme`   | `light` | `light`, `dark` |
| `rainbow` | `false` | `false`, `true` |

## Coding Conventions

- **Python style**: Follow PEP 8. Use single quotes for strings. Use f-strings for string formatting.
- **Docstrings**: All functions should have a concise one-line docstring (`'''Description'''`).
- **Truthy checks for query params**: Query params are strings; check truthiness with `param and param != 'false' and param != '0'` (see `generate_bars` for the established pattern).
- **HTML escaping**: Artist and song names are escaped with `.replace('&', '&amp;')` before being passed to the template.
- **Templates**: Theme-specific styles use `{% if theme == 'dark' %}` conditionals. Keep CSS inside the `<style>` block within the `<foreignObject>`.
- **Timeouts**: All `requests` calls must include a `timeout` argument. Prefer `timeout=10` for new code (the existing code uses `timeout=1000` which is overly long).

## Key Dependencies

- **Flask 2.2.2** – Web framework and Jinja2 templating
- **python-dotenv 0.21.0** – Loads `.env` file into environment variables
- **requests 2.28.1** – HTTP client for Spotify API calls

## Deployment

Deployed on Vercel. The entry point is `api/index.py` exposing the `app` Flask object.
