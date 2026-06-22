# ehsanrahman.com

Personal portfolio site for Ehsan Rahman, Site Reliability Engineer.

## Overview

A single-page static site with a dark "Observatory" dashboard aesthetic. Built with vanilla HTML, CSS, and JavaScript — no frameworks or build step required.

## Structure

| File | Purpose |
|------|---------|
| `index.html` | Main page — all markup, styles, and layout |
| `support.js` | Runtime JS: custom element renderer, canvas radar, clock |
| `.cpanel.yml` | cPanel deployment config |

## Development

Open `index.html` directly in a browser. No build tool or local server needed.

## Deployment

Configured for cPanel via `.cpanel.yml`. Pushing to the repository triggers a file copy to the public_html directory on the server.

## License

See [LICENSE](LICENSE).
