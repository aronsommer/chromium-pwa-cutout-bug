# PWA Camera Cutout Bug – Chromium

Minimal reproduction for a Chromium bug: PWA `display: fullscreen` in the manifest ignores
`viewport-fit=cover` for the camera cutout area on Android.

## Demo

https://aronsommer.github.io/chromium-pwa-cutout-bug/

## Structure

- `standalone-workaround/` — `display: standalone` + JS `requestFullscreen()` on first tap ✅
- `manifest-fullscreen-broken/` — `display: fullscreen` manifest only ❌

## Bug report

See [BUG_REPORT.md](BUG_REPORT.md)
