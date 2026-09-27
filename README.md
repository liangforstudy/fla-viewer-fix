# FLA Viewer

[![Deploy to GitHub Pages](https://github.com/lifeart/fla-viewer/actions/workflows/deploy.yml/badge.svg)](https://github.com/lifeart/fla-viewer/actions/workflows/deploy.yml)
[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Vite](https://img.shields.io/badge/Vite-7.x-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![Vitest](https://img.shields.io/badge/Tested%20with-Vitest-6E9F18?logo=vitest&logoColor=white)](https://vitest.dev/)

A fork of [lifeart/fla-viewer](https://github.com/lifeart/fla-viewer), a browser-based viewer for Adobe Animate/Flash `.fla` files. See the original project for full documentation.

## What this fork changes

[Upstream's support](https://github.com/lifeart/fla-viewer) for old **binary FLA files** (Flash 5 to CS4) was an **incomplete, work-in-progress parser**. It was built from a partly reverse-engineered description of the format and mostly synthetic test files, so several byte layouts were guessed wrong, and sound and tweens were never read. With a real Flash 8 file, the result was a blank white stage with no sound.

This fork completes and corrects that parser. Every change was checked against a **real Flash 8 project and the video Flash exported from it**, until playback matched frame for frame, with sound. The byte-level evidence for each fix is in [BINARY_FLA_FIXES.md](BINARY_FLA_FIXES.md). A few player bugs affecting **all** FLA files were fixed along the way.

**Try it:** click **Sample** on the [live demo](https://liangforstudy.github.io/fla-viewer-fix/) to load the Flash 8 project used to verify these fixes.

### Which Flash versions are covered?

FLA files come in two formats, and the viewer picks a parser from the file's first bytes. CS5+ files go to upstream's main XFL parser, which already worked; its XFL code wasn't changed.

| Saved by | Format | Parser |
|---|---|---|
| Flash 5 → Flash 8 → CS3 → CS4 | Binary | `binary-fla-parser.ts` (fixed here) |
| CS5 and later, Adobe Animate | ZIP + XML (XFL) | `fla-parser.ts` (upstream, unchanged) |

Within the binary range:

| Version | Status | Notes |
|---|---|---|
| Flash 8 | ✅ Tested | Real project, matched frame-for-frame against its exported video, with sound |
| Flash MX 2004 (7) | ✅ Tested | Real sample file renders correctly |
| CS3, CS4 | ❔ Likely, untested | Same format family, but may use newer record layouts; no sample file yet |
| Flash 5, MX (6) | ⚠️ Partial | Artwork should mostly work; animation may collapse to a single still frame (the timeline reader only accepts the Flash 7/8 layer layout) |

Have a real CS3/CS4 (or Flash 5/MX) `.fla`? It's the best way to confirm support. Please share one.

### Fixed: binary FLA files

- **White screen:** layers were misread as hidden guide layers, and every placed symbol pointed at the first library item.
- **Wrong size and position:** Flash 8+ shapes were drawn 2× too large, and the stage size came from a stale publish setting.
- **Missing layers:** only the first layer of each scene was read.
- **Missing colours:** one fill per shape was dropped, and gradient fills broke the whole shape.
- **Wrong stacking:** layers were drawn in reverse order.
- **Stray fill shapes:** thin details such as outlines were joined into large incorrect shapes.
- **No explosion/transition animation:** motion tweens are now read.
- **No audio:** sounds in binary files are now loaded (PCM and MP3).

### Fixed: player (all FLA files, including CS5+ and Animate)

- **Silent "event" sounds:** the player and the video exporter only played "stream" sounds. They now also play "event" sounds (Flash's default).
- **Play/Pause button:** clicks on the icon were often ignored during playback, because the icon was redrawn every frame; only the button's edges worked.
- **Timeline scrubbing:** the timeline only responded to a click on a 4px bar. It now follows a drag, has a larger grab area, and pauses playback while dragging.
- **Duplicate audio:** loading a second file (or the sample again) left the old player running, so two soundtracks played at once and pausing only stopped one.
- **iOS double-tap zoom:** quick taps (e.g. on Play/Pause) zoomed the page on iPhone. Pinch-to-zoom still works.
- **Resetting the view:** double-click the canvas (or press `0`) to reset pan and zoom.

### Not yet supported

**In binary files:** mask layers, frame labels, and the sound sync mode (it is assumed to be "event").

**Next:** sound sync (event/start/stop/stream, loops, trimming), then mask layers. Both are waiting on small Flash 8 (or other pre-CS5) test files; the recipe is in [BINARY_FLA_FIXES.md](BINARY_FLA_FIXES.md#next-sound-sync-then-mask-layers).

Technical details, including the byte-level evidence for each fix: **[BINARY_FLA_FIXES.md](BINARY_FLA_FIXES.md)**.

## Run locally

```bash
npm install
npm run dev     # → localhost:3000
npm test        # 914 tests
```

## License

[ISC](LICENSE) © lifeart
