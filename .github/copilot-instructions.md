# Copilot instructions — tasmota-dashboard

This repository is a minimal, single-page Tasmota device dashboard. Keep changes small and focused — the app is one HTML file with embedded CSS and JS.

**Big picture**
- Single-page static app: [dashboard2.html](dashboard2.html#L1-L400) is the entire application (UI, styles, device configuration and polling logic).
- The page polls Tasmota devices via a local HTTP proxy on port `8080` using paths like `/tasmota/{ip}/cm?cmnd=Status 2` and expects JSON where firmware version is at `StatusFWR.Version`.

**Key patterns & conventions**
- Device config: edit the `devices` array inside [dashboard2.html](dashboard2.html#L1-L400). Entries look like `{ ip: "192.168.x.x", name: "Label", icon: "🔌" }`.
- DOM ids: cards and status elements use index-based ids: `card-{i}`, `status-{i}`, `ver-{i}`. Preserve this indexing when changing rendering logic.
- Visual states use CSS classes `checking`, `online`, `offline`. Keep these names to avoid breaking styles.
- UI language is German (`<html lang="de">`) and most literal strings are German — maintain language consistency for UX.

**Network & integration notes**
- The app does not contact devices directly from the browser; it expects an nginx (or similar) proxy on the same origin that forwards `/tasmota/{ip}/cm?...` to device IPs on the LAN. If the proxy is not running, fetch calls will fail (or CORS may block them).
- Example fetch path constructed in code: `window.location.origin + ':8080/tasmota/' + device.ip + '/cm?cmnd=Status 2'` (see `checkStatus` in [dashboard2.html](dashboard2.html#L1-L400)).

**Local development / debugging**
- No build step. Serve files from a simple HTTP server to match `window.location.origin` behavior:

  - Python: `python -m http.server 8000`
  - Node: `npx http-server -p 8000` or `npx serve .`

- Open the page in a browser and use DevTools → Network to inspect requests to `:8080/tasmota/...` and Console for runtime errors. Increase the fetch timeout if devices are slow (code uses `AbortSignal.timeout`).

**When you edit**
- Prefer minimal diffs: update only `devices` array and small UI tweaks. Large refactors should keep the same public DOM ids and status class names or update CSS accordingly.
- If adding new API calls, follow the same proxy path pattern and JSON expectations; document any new fields you rely on in this file.

**Missing / unknown items to ask the maintainer**
- Provide sample nginx proxy config (host/port) if available.
- Any CI, linting, or formatting rules to enforce?

Please review and tell me if you want more details (proxy config, device schema, or CI instructions).
