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

**Un push a `main` no despliega nada.** No hay CI acá: hasta que alguien corra el
deploy, el repo y producción dicen cosas distintas. Es lo contrario del repo de
la app, donde el push a `main` sí despliega solo.

### `public: "."` publica todo lo que no esté en `ignore`

Y el `ignore` de `firebase.json` tiene cuatro entradas (`firebase.json`,
`.firebaserc`, `.git`, `.gitignore`). Todo lo demás que esté en la carpeta al
momento del deploy **queda servido en `collekta.app`**, tenga o no que ver con el
sitio. Hoy están públicos, verificado con `curl`:

```
collekta.app/CLAUDE.md          200   ← este archivo
collekta.app/PRODUCT.md         200
collekta.app/images/images.zip  200   ← 2.6 MB de assets de trabajo
```

No es hipotético y no hay nada que avise: el deploy imprime `found N files` y ya.
Antes de desplegar conviene mirar qué hay sin trackear en la carpeta — un
borrador, un export, un `.zip` intermedio se publican igual. Si algo no debe
salir, va al `ignore` de `firebase.json`, no al `.gitignore` (Firebase no lee el
`.gitignore`).

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
| `.well-known/assetlinks.json` | Android App Links verification |
| `.well-known/apple-app-site-association` | iOS Universal Links verification |

## Este dominio verifica los deep links de la app

Los dos archivos de `.well-known/` son lo que hace que `https://collekta.app/u/<usuario>`
abra la app en vez del navegador. No son documentación: si se rompen, el link se
degrada a Safari/Chrome **sin error**.

Dos trampas propias de Firebase Hosting:

- **Si el repo no trae un `apple-app-site-association`, Firebase sirve uno propio**
  — y el que sirve es `{"applinks":{"apps":[],"details":[]}}`, que no significa
  "falta configurar" sino "ningún app maneja este dominio". O sea que la ruta
  devuelve 200 y parece sana mientras los universal links están apagados. El
  archivo del repo lo reemplaza; borrarlo no deja el path vacío, lo devuelve a esa
  versión que apaga la feature.
- **Sin extensión, Firebase lo serviría como `octet-stream` y Apple lo rechaza.**
  De ahí la entrada en `headers` de `firebase.json` que fuerza
  `Content-Type: application/json`. Es la misma razón por la que `assetlinks.json`
  tiene la suya.

Los paths declarados (`/u/*`, `/users/*`) tienen que seguir a
`allowedDeepLinkPrefixes` en `cromapp/lib/config/router/deep_links.dart`. Agregar
una ruta linkeable nueva en la app y no acá la deja abriendo el navegador.

**Hay dos cachés de Apple y se confunden.** La del CDN —el archivo que el
dispositivo consulta de verdad— se puede mirar:

```
curl https://app-site-association.cdn-apple.com/a/v1/collekta.app
```

Medido al desplegar este archivo, se actualizó en menos de un minuto. La que sí
estorba es **la del dispositivo**: iOS guarda el AASA al instalar la app y no lo
relee, así que **reinstalar es parte de la prueba** aunque el CDN ya tenga lo
nuevo. Para saltarse ambas durante el desarrollo, el entitlement admite
`applinks:collekta.app?mode=developer`.

Si el CDN sigue sirviendo lo viejo, el deploy no se corrió — ver arriba.

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
