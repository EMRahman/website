# ehsanrahman.com

Personal portfolio site for Ehsan Rahman, Site Reliability Engineer.

## Overview

A single-page static site with a dark "Observatory" dashboard aesthetic. Uses React 18 (loaded at runtime from the unpkg CDN) for component rendering, with vanilla HTML and CSS for layout and styling.

## Structure

| File | Purpose |
|------|---------|
| `index.html` | Main page — all markup, styles, and layout |
| `support.js` | Runtime JS: custom element renderer, canvas radar, clock |
| `.cpanel.yml` | cPanel deployment config |

## Development

Open `index.html` directly in a browser. No build tool or local server needed.

**Note:** The page requires network access to load React 18 and ReactDOM from unpkg at runtime. It will not boot in offline environments or under restrictive CSPs that block `unpkg.com`.

## Deployment

Configured for cPanel via `.cpanel.yml`. Pushing to the repository triggers a file copy to the public_html directory on the server.

## License

See [LICENSE](LICENSE).
