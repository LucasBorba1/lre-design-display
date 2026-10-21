# Pintle Engine PoC: interactive page

A single static page (no build step) that presents the GOX/ethanol pintle engine proof of concept:

- **Live simulator**: P&ID with animated flow, Arm / Fire / Safe / E-stop, throttle, phase one (240 N) and phase two (400 N), injectable faults, automatic aborts, live chart and event log. Click any component for a pop-up with a drawing, its description and a link to real photos; click a valve again to open or close it by hand.
- **3D engine** (three.js): orbit, click a part to fly the camera to it, exploded view that follows the assembly order (Play assembly animates it), half section, hot fire.
- **English / Portuguese** switch in the header. It remembers the choice per browser, picks Portuguese automatically for pt browsers, and `#pt` or `#en` at the end of the link forces a language.
- Design choices, fail-safe and abort tables, the 12-step test campaign, and the comparison with the lander engine.

## Put it on Vercel

Option A, command line (needs Node.js):

```bash
cd lre-design-display
npx vercel          # first time: log in, accept the defaults
npx vercel --prod   # publish to the production URL
```

Option B, GitHub: push this folder to a repository, then in Vercel choose **Add New → Project**, import the repo, set the framework preset to **Other**, and deploy. Every push redeploys.

Option C: drag this folder onto the Vercel dashboard's "Deploy" drop area.

To try it locally first: `npx serve .` and open http://localhost:3000.

## Updating it

Everything lives in `index.html`: styles at the top, the simulator script in the middle, the three.js module at the bottom.

- Design numbers for the simulator are in the `PH` constant (`PH = {1: {...}, 2: {...}}`).
- Component descriptions for the schematic are in the `INFO` table, and the pop-up drawings are in `ART`.
- The 3D parts are built with `lathe(...)` profiles in millimetres; each `add(...)` call is one entry in the part list, and its last argument lists the exploded-view moves. The assembly captions are in `STEPS`.

## Translations

All Portuguese text lives in the `window.PT_BR` object near the bottom of `index.html`, keyed by the exact English text. To change a translation, edit its value. If you change an English sentence, change its key too, or that sentence falls back to English.

## Component photos

See `photos/README.md`: a photo named after the component type replaces the drawing in the pop-up.

## Swapping in SolidWorks models later

1. In SolidWorks, export each part (or the assembly) as **glTF/GLB** (SolidWorks 2023+ has it under Save As; otherwise export STEP and convert in Blender: File → Import STEP via add-on, then Export glTF 2.0 / .glb). Keep units in millimetres, with the engine axis along Y.
2. Put the files in `poc-site/models/`.
3. In the module script, add `import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js';` and replace a part's meshes with the loaded scene, keeping the same `add(key, n, name, desc, meshes, dir)` call so the exploded view, section and part list keep working.

Libraries load from cdn.jsdelivr.net (three@0.160.0) and fonts from Google Fonts, so the page needs internet access when presented.
