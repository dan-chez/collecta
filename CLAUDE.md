# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Static website for **Collecta** — the landing page and legal documents for a Latin American collectibles app. Hosted on Firebase Hosting (static, no server).

## Deploy

```bash
firebase deploy --only hosting
```

This deploys to `https://cromapp-100b8.web.app`. No build step — files are served as-is.

> Do NOT use Firebase App Hosting (Cloud Buildpacks). This is a static site with no `package.json`.

## Files

| File | Purpose |
|------|---------|
| `index.html` | Landing page (hero, features, brands, CTA) |
| `politica-de-privacidad.html` | Privacy policy |
| `terminos-y-condiciones.html` | Terms and conditions |
| `eliminar-cuenta.html` | Account deletion instructions |
| `app-ads.txt` | AdMob app-ads verification |
| `icono_collecta.png` | App icon used in nav, hero, and footer |
| `screenshot.png` | App screenshot shown inside phone mockup |

## Design system

All pages share the same CSS variables defined inline in each file:

```css
--dark-base:      #080D16   /* root background */
--dark-surface:   #0F1622   /* headers, cards */
--dark-elevated:  #141C2D   /* inputs, bottom sheets */
--primary:        #4338CA   /* buttons, active states */
--primary-light:  #6366F1   /* hover states */
--gold:           #D4A843   /* brand accent (Collecta logo, stats) */
--teal:           #00C4A8   /* owned/progress status only */
--border:         rgba(255,255,255,.07)
```

Background blobs use `rgba(67,56,202,.07–.14)` + `blur(60px)`. No neon glows.

## Google Search Console

The verification meta tag is in `index.html` line 6. Update it there when reverifying.
