# FLA Viewer

[![Deploy to GitHub Pages](https://github.com/lifeart/fla-viewer/actions/workflows/deploy.yml/badge.svg)](https://github.com/lifeart/fla-viewer/actions/workflows/deploy.yml)
[![License: ISC](https://img.shields.io/badge/License-ISC-blue.svg)](https://opensource.org/licenses/ISC)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.x-3178C6?logo=typescript&logoColor=white)](https://www.typescriptlang.org/)
[![Vite](https://img.shields.io/badge/Vite-7.x-646CFF?logo=vite&logoColor=white)](https://vitejs.dev/)
[![Vitest](https://img.shields.io/badge/Tested%20with-Vitest-6E9F18?logo=vitest&logoColor=white)](https://vitest.dev/)

A fork of [lifeart/fla-viewer](https://github.com/lifeart/fla-viewer), a browser-based viewer for Adobe Animate/Flash `.fla` files. See the original project for full documentation.

## What this fork changes

Old **binary FLA files** (Flash CS4 and earlier) opened as a **blank white stage with no sound**. They now play with their artwork, animation and audio.

**Fixed**

- **White screen:** layers were misread as hidden guide layers, and every placed symbol pointed at the first library item.
- **Wrong size and position:** Flash 8+ shapes were drawn 2× too large, and the stage size came from a stale publish setting.
- **Missing layers:** only the first layer of each scene was read.
- **Missing colours:** one fill per shape was dropped, and gradient fills broke the whole shape.
- **Wrong stacking:** layers were drawn in reverse order.
- **Stray fill shapes:** thin details such as outlines were joined into large incorrect shapes.
- **No explosion/transition animation:** motion tweens are now read.
- **No audio:** sounds in binary files are now loaded (PCM and MP3).
- **Silent "event" sounds:** the player and the video exporter only played "stream" sounds. They now also play "event" sounds (Flash's default), in all FLA files.

**Still not supported in binary files:** mask layers, frame labels, and the sound sync mode (it is assumed to be "event").

**Next:** sound sync (event/start/stop/stream, loops, trimming), then mask layers. Both are waiting on small Flash CS4 test files; the recipe is in [BINARY_FLA_FIXES.md](BINARY_FLA_FIXES.md#next-sound-sync-then-mask-layers).

Technical details, including the byte-level evidence for each fix: **[BINARY_FLA_FIXES.md](BINARY_FLA_FIXES.md)**.

## Run locally

```bash
npm install
npm run dev     # → localhost:3000
npm test        # 914 tests
```

## License

[ISC](LICENSE) © lifeart
