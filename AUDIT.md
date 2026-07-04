# Audit — ehsanrahman.com (single-pass, 2026-07-04)

Scope: `index.html` (template + inline DC logic), `support.js` (generated dc-runtime),
`.cpanel.yml`. Not audited: `vendor/` (stock React 18.3.1 UMD), images, docs.

## Top 5 (ranked)

1. **Blank site on any boot failure.** `support.js:1428` hides all content (`x-dc{display:none!important}`)
   before React is confirmed loaded; the failure path (`support.js:1509-1512`) just rethrows, so if
   `vendor/react*.js` 404s and the unpkg fallback is blocked, every visitor gets a permanently blank page.
2. **No `<title>` / meta description / favicon / OG tags.** `index.html:3-12` — the real `<head>` has only
   charset, viewport, and scripts; search snippets, browser tab, and link previews are all degraded for a
   site whose purpose is being found.
3. **Animation never stops.** Two canvases redraw at 60 fps (`index.html:1300-1318`) and a `setInterval`
   full-page re-render fires every 800 ms (`index.html:889`, `tick()` at 907) even when the hero is scrolled
   off-screen — i.e. the whole time someone reads the ACI section — burning CPU/battery on mobile.
4. **The entire render path is generated code with no source in the repo.** `support.js:1` says
   "GENERATED from dc-runtime/src/*.ts — do not edit. Rebuild with `cd dc-runtime && bun run build`" but
   `dc-runtime/` doesn't exist here; any runtime bug is unfixable except by hand-editing a build artifact.
   Side effect: `boot()` re-downloads the full page on every load (`support.js:154`) — and that refetch is
   load-bearing (it restores camelCase attrs like `tabIndex`/`strokeWidth` that DOM parsing lowercases), so
   it can't be deleted without the source.
5. **Deploy publishes the whole repo.** `.cpanel.yml:5` `cp -R …/github_website/* public_html/` ships
   `docs/aci-notes-plan.md`, `README.md`, `LICENSE` to the live site and never deletes removed files —
   draft notes become public URLs and stale files linger forever.

## Checklist (execute top-down)

### P0 — resilience & discoverability
- [ ] Un-hide content on boot failure: give the `hideRawTemplate()` style element an id (`support.js:1429`)
      and remove it in the `.catch` at `support.js:1509` so a failed React load degrades to readable raw
      HTML instead of a blank page. Verify by blocking `vendor/` + unpkg in devtools and reloading.
- [ ] Add `<title>` and `<meta name="description">` to the real `<head>` (`index.html`, after line 5).
- [ ] Add a favicon (the SVG in `#__bundler_thumbnail`, `index.html:69`, is a ready-made mark — inline it
      as a `data:` icon) and `og:title` / `og:description` / `og:image` tags.
- [ ] Scope the deploy copy in `.cpanel.yml:5` to `index.html support.js images/ vendor/` only; confirm
      `https://ehsanrahman.com/docs/aci-notes-plan.md` stops resolving after the next deploy.

### P1 — performance
- [ ] Pause the animation when the hero is off-screen: set `this._heroVisible` via an
      `IntersectionObserver` on `.hero-shell` in `componentDidMount` (`index.html:879`), early-return from
      `tick()` (`index.html:907`) when `document.hidden || !this._heroVisible`, and skip the draw body in
      `frame` (`index.html:1301`) the same way (still re-arm `requestAnimationFrame` and refresh `last`).
      Disconnect the observer in `componentWillUnmount` (`index.html:891`).
      Verify: scroll to the ACI section, check CPU in the performance monitor drops to ~0.
- [ ] Move the Google Fonts `<link rel="preconnect">`/stylesheet from the in-template `<helmet>`
      (`index.html:16-18`) into the real `<head>` so font loading isn't gated on the JS boot (fonts
      currently start downloading only after React mounts → late FOUT).
- [ ] Note for later: every `setState` (clock tick, gloss hover at `index.html:865-877`) rebuilds the full
      ~825-line template into React elements and re-diffs. Structural fix belongs in dc-runtime
      (see P2 decision), not worth local surgery.

### P2 — architecture (needs owner decision)
- [ ] Recover and commit the `dc-runtime` source (or pin it as a submodule), OR declare `support.js` a
      hand-maintained vendored file and delete the misleading "do not edit / rebuild" header. Until then
      every runtime fix is an edit to generated output.
- [ ] Once the source exists: precompile the template case-encoding (or stop shipping it twice) so
      `boot()`'s `fetch(location.href)` self-refetch (`support.js:154`) can be dropped — it currently
      doubles page transfer on every visit.
- [ ] Decide on CSP: the runtime `new Function`s inline script content (`support.js:688,1026`), which
      forces `'unsafe-eval'` and blocks meaningful CSP hardening. Low urgency for a static personal site.

### P3 — minor / cosmetic
- [ ] `onTouchEnd`'s 1.4 s `setTimeout` (`index.html:939`) isn't cleared in `teardown()` — harmless on a
      page that never unmounts, one-line fix while in the area.
- [ ] The converging-pair vignette re-logs `MAYDAY` every 16 s cycle (`index.html:991,1134`) and QFA12's
      fuel narrative ends permanently after 80 s (`index.html:993,1140`), silently dropping the KNOWN
      count by one — confirm both are intentional theatre; if not, reset the cycle.
- [ ] Add `aria-hidden="true"` to the two decorative canvases (`index.html:131,141`) and consider a real
      `<h1>` (the page currently opens at `<h2>`; the hero name at `index.html:114` is a `div`).
- [ ] `mailto:mail@ehsanrahman.com` is plain text in the HTML (`index.html:829`) — fine if you accept
      scraper spam; obfuscate only if it becomes a problem.

## Verification after any fix
Serve locally (`python3 -m http.server`) and check: page renders, radar + map animate, gloss popovers
open on hover *and* tap, event log scrolls, no console errors, then re-test with `vendor/` blocked.
