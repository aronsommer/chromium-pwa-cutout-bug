# Bug Report: PWA `display: fullscreen` ignores `viewport-fit=cover` for camera cutout

**Chromium issue tracker:** https://issues.chromium.org

---

## Summary

A PWA with `display: fullscreen` in the manifest shows a black bar in the camera cutout
(punch-hole) area on Android, even when `viewport-fit=cover` is set in the viewport meta tag.
The same app using `requestFullscreen()` via JavaScript correctly renders content behind the
cutout. Both should behave identically.

---

## Steps to reproduce

1. Open the demo on an Android device with a camera cutout: **https://aronsommer.github.io/chromium-pwa-cutout-bug/**
2. Open **"Broken – display: fullscreen (manifest only)"**, tap **"Install PWA"**, and launch from the home screen
3. Observe the black bar at the top covering the camera cutout area

---

## Expected behavior

Content renders behind the camera cutout (as specified by `viewport-fit=cover`), matching the
behavior of `requestFullscreen()`. The camera cutout zone should show the app's background — not
a system-painted black bar.

---

## Actual behavior

A black bar covers the camera cutout area. The rest of the screen renders correctly
(no black bar at bottom where there is no cutout), confirming `viewport-fit=cover` is partially
respected but the cutout zone is blocked.

---

## Comparison

| Approach                                                               | Cutout renders? |
| ---------------------------------------------------------------------- | --------------- |
| `display: standalone` + JS `requestFullscreen()` on first user gesture | ✅ Yes          |
| `display: fullscreen` in manifest (with `viewport-fit=cover`)          | ❌ Black bar    |

---

## Root cause (suspected)

`requestFullscreen()` sets `LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES` on the Android window,
allowing web content to render behind the cutout. Manifest `display: fullscreen` uses a
different Android window cutout mode (`LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT` or `NEVER`),
which blocks rendering in the cutout zone regardless of `viewport-fit=cover`.

---

## Live demo

**https://aronsommer.github.io/chromium-pwa-cutout-bug/**

- `standalone-workaround/` — install via button, tap once after launch → cutout works correctly ✅
- `manifest-fullscreen-broken/` — install via button, launch → black bar at cutout ❌

---

## Minimal reproduction

`viewport-fit=cover` in viewport meta tag (both examples):

```html
<meta
  name="viewport"
  content="width=device-width, initial-scale=1.0, viewport-fit=cover"
/>
```

Broken — manifest only:

```json
{ "display": "fullscreen" }
```

Working — standalone + JS:

```json
{ "display": "standalone" }
```

```js
document.addEventListener(
  "click",
  () => {
    if (!document.fullscreenElement) {
      document.documentElement.requestFullscreen().catch(() => {});
    }
  },
  { once: true },
);
```

---

## Environment

- **Device:** Samsung Galaxy S25 (center punch-hole camera)
- **Android version:** [fill in]
- **Chrome version:** [fill in]
