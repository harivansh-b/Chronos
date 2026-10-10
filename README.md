# CHRONOS — An Immersive Time Machine Web Experience

A fictional, scroll-driven science-fiction experience built with HTML, CSS, vanilla ES modules,
Three.js, GSAP + ScrollTrigger and Lenis. All readouts, specs and mission entries are imaginary.

## Run

Libraries load as pinned ES modules from jsDelivr (`src/libs.js`), so there is no bundling step.

```bash
cd chronos
npm install      # installs Vite (dev server only)
npm run dev      # http://localhost:5173
```

No Node? Any static server works, e.g. `python -m http.server 5173` inside `chronos/`.
(Opening `index.html` via `file://` will not work — ES modules need http.)

## Structure

```
index.html                 semantic markup for all 9 sections
src/main.js                bootstraps everything, owns navigation-to-era logic
src/libs.js                pinned CDN imports (three 0.160, gsap 3.12.5, lenis 1.1.13)
src/data.js                eras, machine components, mission log
src/style.css              design system, layouts, responsive + reduced-motion rules
src/three/scene.js         renderer, camera director, single render loop, raycast + projection
src/three/timeMachine.js   procedural reactor (core, rings, stabilizer, nav core, anchor, portal shader)
src/three/eras.js          five procedural era environments (instanced trees/towers, gears, pyramids…)
src/three/particles.js     stars, dust, warp streaks, anomaly particle shader
src/three/interactions.js  hotspots, picking, drag-rotate, spec panel
src/three/eraInteractions.js  explorable eras: drag/arrow-key free-look, landmark beacons, inspect card
src/animations/scroll.js   Lenis ⇄ ScrollTrigger sync, scrubbed scenes, reveals
src/animations/transitions.js  boot sequence, hero entrance, flash
src/ui/navigation.js       nav, mobile menu, progress, soundtrack toggle (drone fallback)
src/ui/timeline.js         journey HUD + destination panels
src/ui/missionControl.js   console validation, diagnostics, jump
```

## How sync works

`#journey` is a 700vh section with a `position: sticky` stage. One scrubbed ScrollTrigger writes
`state.journey` (0–1). The render loop derives camera, ring speed, portal scale, warp intensity
and active era from that single value, and calls back to the UI only when the era changes.
Timeline, destination cards and Mission Control all convert an era index to a scroll position
(`eraProgress(i)`) and let Lenis scroll there, so the scene is always driven by real scroll.

## Interactive eras

Inside each era of the journey you can drag (or use ←/→) to look around the 3D scene while
scrolling still drives the camera path. Pulsing beacons track landmarks (the sauropod, the Great
Pyramid, the Great Escapement gear, Meridian Spire, the ringed planet…); click a beacon or the mesh
itself to turn toward it and open a scan card. Landmark copy lives in `LANDMARKS` in `src/data.js`.

## Audio

The speaker button streams the soundtrack set in `SOUNDTRACK` (`src/data.js`):
[Outer Space Loop by wipics](https://opengameart.org/content/outer-space-loop), CC0. It loads only
on first click. If the file can't play, the generative Web Audio drone is used instead. Swap the
`src` for your own track (e.g. `./audio/theme.mp3`) to change it.

## Fallbacks

- No WebGL → canvas removed, gradient background, components listed as buttons; all UI still works.
- `prefers-reduced-motion` → no boot, no warp streaks/shake, instant scroll jumps, no reveal motion.
- Mobile / ≤4 cores → fewer particles, lower pixel ratio, no MSAA, shorter pinned sections.
