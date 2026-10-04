# AGENTS.md

## Project Overview
Single-file static website: `index-2.html` — a self-contained "Neural Core" 3D page using Three.js (loaded from cdnjs CDN). No backend, no build step, no package manager, no external API credentials.

## Running in Base44
- Served by `nginx:alpine` via `docker-compose.base44.yml` on host port 3000.
- The repo is bind-mounted read-only at `/usr/share/nginx/html`; nginx config (`nginx.base44.conf`) sets `index-2.html` as the index.
- Edits to `index-2.html` are visible on browser refresh (no build step, no hot reload).

## Notes / Quirks
- The host `/app` directory must be world-traversable (chmod 755) or nginx's non-root worker gets 403 Forbidden. If a fresh checkout shows 403, run `chmod 755 /app`.
- The nginx healthcheck must use `127.0.0.1` (not `localhost`) — nginx listens on IPv4 only and `localhost` resolves to IPv6 `::1` inside the alpine container.
- Three.js is loaded from `https://cdnjs.cloudflare.com/...`; the page needs outbound internet for the 3D scene, but falls back to a static HUD if it fails.

## Verify
- `curl -s -o /dev/null -w "%{http_code}" http://localhost:3000/` → 200
- `docker compose -f docker-compose.base44.yml ps` → web service `healthy`
