# PWA Setup

## Overview

TaxSetu is a fully offline-capable Progressive Web App. Both the Web App Manifest and Service Worker are generated dynamically as Blob URLs to avoid needing separate files on the server.

## Dynamic Manifest Injection

Located in the `<head>` section of `taxsetu_pwa_master_file.html`.

```js
const manifestData = {
  "short_name": "TaxSetu",
  "name": "TaxSetu Capital Gains Auditor",
  "icons": [
    {
      "src": "data:image/svg+xml;utf8,...",  // SVG checkmark in green circle
      "sizes": "192x192",
      "type": "image/svg+xml",
      "purpose": "any maskable"
    },
    // ...512x512 variant
  ],
  "start_url": ".",
  "background_color": "#ffffff",
  "theme_color": "#ffffff",
  "display": "standalone",
  "orientation": "portrait"
};

// Blob URL generation
const blob = new Blob([JSON.stringify(manifestData)], {type: 'application/json'});
const manifestURL = URL.createObjectURL(blob);
const link = document.createElement('link');
link.rel = 'manifest';
link.href = manifestURL;
document.head.appendChild(link);
```

**Key details**:
- Icons are inline SVG data URIs (a green circle with a white checkmark)
- `display: standalone` enables full-screen PWA mode
- `orientation: portrait` locks to portrait on mobile

## Dynamic Service Worker

Registered immediately after manifest injection. Uses a cache-first strategy.

```js
const swCode = `
  const CACHE_NAME = 'taxsetu-v1';
  const ASSETS = ['/', '/index.html'];

  self.addEventListener('install', (e) => {
    e.waitUntil(
      caches.open(CACHE_NAME).then(cache => cache.addAll(ASSETS))
    );
  });

  self.addEventListener('fetch', (e) => {
    e.respondWith(
      caches.match(e.request).then(cached => cached || fetch(e.request))
    );
  });
`;

const swBlob = new Blob([swCode], { type: 'application/javascript' });
const swURL = URL.createObjectURL(swBlob);
navigator.serviceWorker.register(swURL);
```

**Cache-first strategy**: The service worker serves cached responses when available, falling back to the network. This enables full offline functionality after the first visit.

## Sandbox Detection

Service Worker registration is **skipped** in sandboxed environments where Blob URL service workers are blocked:

```js
const isSandbox = window.location.hostname.includes('usercontent.goog') ||
                  window.location.hostname.includes('webcontainer.io') ||
                  window.location.protocol === 'blob:';

if (!isSandbox) {
  // Register service worker
}
```

This prevents console errors in environments like:
- `usercontent.goog` — Google-hosted previews
- `webcontainer.io` — StackBlitz/CodeSandbox environments
- `blob:` protocol — Local file previews in some browsers

## Standalone Detection

The app detects whether it's running as a standalone PWA to control the install guide display:

```js
const isStandalone = window.matchMedia('(display-mode: standalone)').matches ||
                     window.navigator.standalone;
```

- `window.matchMedia('(display-mode: standalone)')` — Android/Chrome PWA detection
- `window.navigator.standalone` — iOS Safari "Add to Home Screen" detection
- Either being `true` means the app is installed and running as a PWA

## PWA Install Guide Drawer

If the app is **not** running in standalone mode, a slide-up drawer appears on first visit with instructions:
1. Tap the Share/Action button in your browser
2. Scroll down and tap "Add to Home Screen"
3. Tap "Add" in the top-right corner

## Deployment Notes

- **Single file deployment**: Upload `taxsetu_pwa_master_file.html` to any static host (GitHub Pages, Netlify, Vercel, etc.)
- **No additional files needed**: Manifest and service worker are generated at runtime
- **HTTPS required**: Service Workers only register on HTTPS (or localhost)
- **Cache versioning**: The cache name (`taxsetu-v1`) should be incremented on significant updates to force re-caching

## Current Limitations

- No `beforeinstallprompt` event handler (Chrome's install banner is not intercepted)
- Service worker cache only contains `/` and `/index.html` — no other assets to cache since everything is in one file
- No periodic background sync or push notifications