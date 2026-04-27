# WebTransformation

> Side-by-side comparison of two website versions, with a draggable divider you slide to reveal one over the other.

A single-file, zero-build, zero-dependency tool for comparing two URLs visually. Drop the old version in one input, the new version in the other, and slide the divider in the middle — left side shows one site, right side shows the other, in the same viewport position. Perfect for showing clients a redesign, sanity-checking your own work against staging, or comparing competitors.

**Live demo:** https://cyberdemigods.github.io/web-transformation/

Built by [CyberDemigods](https://cyberdemigods.com).

---

## Features

- **Draggable divider** — mouse, touch, or keyboard (`←` `→`, with `Shift` for 5% jumps, `Home`/`End` for edges)
- **Viewport emulator** — switch between Desktop, Tablet (768px), and Mobile (390px) widths to test responsive behaviour
- **Shareable links** — Copy link button generates a URL with both addresses preconfigured (`?a=...&b=...&vp=tablet&lang=en`), great for sending clients a ready-to-open comparison
- **Load time tracking** — measures time from request to `load` event for each iframe (best-effort: cross-origin iframes don't expose the full Performance API)
- **i18n** — English and Polish UI, switchable from the toolbar; persisted in `localStorage`; auto-detected from `navigator.language`
- **Swap & clear** — quickly swap which URL is on which side, or reset
- **Toast notifications** — non-intrusive feedback instead of `alert()`
- **No tracking, no analytics, no build step** — opens straight from the filesystem

## Tech

Pure vanilla HTML / CSS / JavaScript in a single `index.html` file. Uses:

- `iframe` × 2 stacked with `clip-path: inset(...)` on the right one to reveal the left underneath. Modern browsers respect `clip-path` for hit-testing too, so clicks land on the visible side without proxy events.
- `pointer-events: none` toggled on iframes during drag, otherwise cross-origin iframes swallow `mousemove` and the divider locks up when the cursor enters them.
- `localStorage` for language preference, URL params for shareable state.
- No external libraries. No npm. No build. Just open `index.html`.

## Known limitations

### iframe embedding restrictions

Many production websites block being loaded inside an `iframe` via `X-Frame-Options: DENY`/`SAMEORIGIN` headers or CSP `frame-ancestors`. When that happens, you'll see a blank white area instead of the page, with a console error like *"Refused to display in a frame"*. This is a browser security feature and cannot be bypassed client-side. Workarounds (not implemented):

- Server-side proxy that strips the offending headers (legally grey, depending on your use case)
- Headless browser screenshots instead of live iframes (loses interactivity)

### No scroll synchronisation

Scrolling one side does not scroll the other, because cross-origin iframes do not expose their `contentWindow` to the parent. We could implement scroll sync for same-origin pairs (e.g. staging vs production of your own site) via `postMessage`, but for arbitrary URLs it's not possible. In practice the two sites usually have different page heights anyway, so a synced scroll would drift quickly.

## Getting started

### As a user

1. Visit https://cyberdemigods.github.io/web-transformation/
2. Paste two URLs (auto-prepends `https://` if missing)
3. Hit **Compare** and drag the divider

### As a developer

```bash
git clone https://github.com/CyberDemigods/web-transformation
cd web-transformation
# That's it — open index.html in any modern browser
xdg-open index.html  # or `open` on macOS, `start` on Windows
```

There is no build step. Edit `index.html`, refresh the browser, you're done.

To serve over HTTP for testing (recommended on some browsers when iframe issues arise):

```bash
python3 -m http.server 8080
# then visit http://localhost:8080
```

## URL parameters

The app accepts these query parameters for deep-linking and embedding:

| Param  | Values                       | Purpose                                  |
| ------ | ---------------------------- | ---------------------------------------- |
| `a`    | URL                          | Old / left-side site                     |
| `b`    | URL                          | New / right-side site                    |
| `vp`   | `desktop`, `tablet`, `mobile`| Viewport width preset                    |
| `lang` | `en`, `pl`                   | UI language (overrides browser default)  |

Example: `https://cyberdemigods.github.io/web-transformation/?a=https://example.com&b=https://example.org&vp=tablet&lang=pl`

## Keyboard shortcuts

| Key                            | Action                          |
| ------------------------------ | ------------------------------- |
| `←` / `→`                      | Move divider by 1%              |
| `Shift` + `←` / `→`            | Move divider by 5%              |
| `Home`                         | Snap divider to far left (0%)   |
| `End`                          | Snap divider to far right (100%)|
| `Enter` (in URL field)         | Trigger comparison              |

## Browser support

Tested on recent Chrome, Firefox, Edge, and Safari. Requires support for:

- `clip-path: inset()` (all evergreen browsers)
- `Pointer Events` and `Touch Events`
- `localStorage`, `URLSearchParams`, `navigator.clipboard.writeText`

The `clipboard.writeText` call has a `document.execCommand('copy')` fallback for older / restrictive contexts, and a final `prompt()` if even that fails.

## Roadmap

- [ ] Horizontal split mode (top/bottom instead of left/right)
- [ ] Fade transition between A and B (slider controls opacity)
- [ ] Visual diff overlay highlighting changed regions
- [ ] PNG / PDF export of the current divider position
- [ ] Optional integration with [Lachesis](https://github.com/CyberDemigods/lachesis) — show SEO/performance/accessibility audit metrics for both sides under the slider

## Contributing

Issues and PRs welcome. The codebase is intentionally one file, so the contribution surface is small and obvious. Please keep new dependencies to zero.

## License

MIT — see [LICENSE](./LICENSE).

---

*Forged by [CyberDemigods](https://cyberdemigods.com).*
