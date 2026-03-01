# Copilot Instructions for Spotify-Readme

## Project Overview

Spotify-Readme is a Python Flask web application that generates a dynamic SVG widget showing what you're currently listening to (or recently listened to) on Spotify. The widget is designed to be embedded in GitHub README files.

## Architecture

- **`api/index.py`** – The main Flask application. Handles routing, Spotify API calls, and SVG rendering via Jinja2 templates.
- **`api/templates/index.html`** – The Jinja2 SVG template that renders the widget. Despite the `.html` extension, the output is SVG.
- **`api/base64/`** – Contains text files with base64-encoded fallback images (placeholder album art, scan code, and Spotify logo).
- **`api/requirements.txt`** – Python dependencies: Flask, python-dotenv, and requests.

## Environment Variables

The app requires three environment variables (typically stored in a `.env` file, which is gitignored):

- `CLIENT_ID` – Spotify app client ID
- `CLIENT_SECRET` – Spotify app client secret
- `REFRESH_TOKEN` – Spotify OAuth refresh token

## How to Run Locally

```bash
pip install -r api/requirements.txt
python api/index.py
```

The app runs on `http://localhost:5000` by default (Flask debug mode).

## Widget Query Parameters

The single endpoint (`/`) accepts these query parameters:

| Parameter | Default | Values          |
|-----------|---------|-----------------|
| `spin`    | `false` | `false`, `true` |
| `scan`    | `false` | `false`, `true` |
| `theme`   | `light` | `light`, `dark` |
| `rainbow` | `false` | `false`, `true` |

## Coding Conventions

- **Python style**: Follow PEP 8. Use single quotes for strings. Use f-strings for string formatting.
- **Docstrings**: All functions should have a concise one-line docstring (`'''Description'''`).
- **Truthy checks for query params**: Boolean-like query parameters are strings; check with `param and param != 'false' and param != '0'` (see `generate_bars` for the established pattern).
- **HTML escaping**: Artist and song names are escaped with `.replace('&', '&amp;')` before being passed to the template.
- **Templates**: The SVG template uses Jinja2. Theme-specific styles use `{% if theme == 'dark' %}` conditionals. Keep CSS inside the `<style>` block within the `<foreignObject>`.
- **Timeouts**: All `requests` calls should specify a timeout. The existing code uses `timeout=1000` (seconds), but prefer a lower value such as `10` for new code to avoid long hangs if the Spotify API is unresponsive.

## Key Dependencies

- **Flask 2.2.2** – Web framework and Jinja2 templating
- **python-dotenv 0.21.0** – Loads `.env` file into environment variables
- **requests 2.28.1** – HTTP client for Spotify API calls

## Deployment

The app is deployed on Vercel. The entry point for Vercel is `api/index.py` with the Flask `app` object.

## Testing

There is no automated test suite currently. When adding features, manually verify by running the app locally and checking the rendered SVG output.
