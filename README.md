# ZenVue — Landing Page

The public marketing site for **ZenVue**, a SaaS IoT telemetry platform by
**Zenith Solutions Limited**. ZenVue sells remote asset monitoring as a managed
service — live telemetry, intelligent alerts, and dashboards delivered securely
to any device, anywhere.

This repository contains only the public landing page (`zenvue.app`). It is a
marketing site: no e-commerce, no user management. The customer dashboard
(Grafana) lives separately at `track.zenvue.app`, and the **Login** button on
this site links straight to it.

> **Tagline:** *Real-time remote monitoring of your assets*

---

## Tech stack

Pure **HTML + CSS + vanilla JavaScript**. No build tools, no framework, no npm.
This keeps deployment to a single step: copy static files behind Nginx.

```
.
├── index.html        Main page — all sections
├── css/
│   └── style.css     All styles
├── js/
│   └── main.js       Mobile nav, scroll-reveal, scroll-spy
├── images/           Placeholder dir — imagery TBD (WebP, ≤200 KB each)
└── README.md
```

---

## Local development

No build step. Just open the file in a browser:

```bash
# macOS
open index.html

# Windows
start index.html

# Linux
xdg-open index.html
```

For live-reload while editing, any static server works, e.g.:

```bash
npx serve .        # or: python3 -m http.server 8000
```

Then visit `http://localhost:8000`.

---

## Deployment (Nginx on DigitalOcean Droplet)

The site is served as static files by Nginx (Ubuntu Droplet, Singapore region).
The repository lives on the Droplet at `/opt/zenvue-site`.

1. Pull the latest `main` to `/opt/zenvue-site`.
2. Point an Nginx `server` block at it:

```nginx
server {
    listen 80;
    server_name zenvue.app www.zenvue.app;
    return 301 https://$host$request_uri;          # HTTP → HTTPS
}

server {
    listen 443 ssl http2;
    server_name zenvue.app www.zenvue.app;

    root /opt/zenvue-site;
    index index.html;

    ssl_certificate     /etc/letsencrypt/live/zenvue.app/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/zenvue.app/privkey.pem;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

3. Issue/renew SSL with Certbot (Let's Encrypt):

```bash
sudo certbot --nginx -d zenvue.app -d www.zenvue.app
```

4. Reload Nginx: `sudo nginx -t && sudo systemctl reload nginx`

> `track.zenvue.app` is a **separate** Nginx config that proxies to Grafana on
> port 3000 — not part of this repository.

---

## Brand reference

The palette and assets are aligned with the parent brand, **Zenith Solutions**
(`zenithsuperyachts.com`), so the two sites read as one family. Logo and favicon
assets in `images/` are copied from the `zenith-superyachts` repo.

### Colour palette

| Token         | Hex                     | Usage                        |
| ------------- | ----------------------- | ---------------------------- |
| `--navy`      | `#081826`               | Page background              |
| `--navy-band` | `#07131e`               | Alternating section bands    |
| `--navy-foot` | `#06111a`               | Footer                       |
| `--panel-a/b` | `#0c2234` / `#0a1b2b`   | Gradient panels (hero, CTAs) |
| `--gold`      | `#c8a96b`               | Primary accent, CTAs         |
| `--cream`     | `#f4ead7` / `#f8f4ec`   | Headings (warm cream)        |
| `--text`      | `rgba(255,255,255,.78)` | Body text                    |
| `--line`      | `rgba(255,255,255,.1)`  | Borders                      |

### Typography

- **All text:** [Hanken Grotesk](https://fonts.google.com/specimen/Hanken+Grotesk) — 300/400/500/600/700

A single modern grotesk, loaded from Google Fonts with `preconnect`. Cream
headings, white-opacity body — matching the Zenith reference's clean sans-serif feel.

### Design principles

Modern, calm, and concise. Rounded corners (14–30px), soft `0 20px 50px` shadows,
gradient panels, generous whitespace. Gold used as a quiet accent. The page is
deliberately lean and enquiry-led — **no performance, protocol, or method claims**;
functionality is teased rather than spec-sheeted. Two clear paths: **Client login**
(→ `track.zenvue.app`) for existing clients, **Request information** (mailto) for
prospects. Subtle fade-up reveals only, wrapped in
`@media (prefers-reduced-motion: no-preference)`.

---

© 2026 Zenith Solutions Limited — All rights reserved.
