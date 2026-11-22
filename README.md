# Rigsmith

**Build a PC in 3D, one part at a time.** Pick real hardware, install every component by hand, plug in every cable, press the power button and see how your build performs.

Rigsmith is a browser-based PC building simulator and learning tool. It runs entirely in the browser with no backend, no build step and no install.


## Features

- **Hands-on 3D assembly.** Drag parts from the catalog onto green outlines in the case. Parts snap into place with an install animation. Mounts that won't take a part turn red and explain why ("This cooler is 168 mm tall; the case allows 155 mm").
- **Real build order.** Assemble the CPU, cooler, memory and M.2 drive on an anti-static mat, then carry the whole motherboard into the case.
- **Manual cable management.** Pull modular cables out of the power supply, drag each free end to its socket, and route cables through grommets behind the motherboard tray. Connectors are keyed: a PCIe cable is refused by the CPU socket, with an explanation of why that matters.
- **Compatibility checking** from real specifications: socket and chipset support, BIOS notes, DDR4 vs DDR5, memory slots and capacity, GPU length and thickness, cooler height, radiator mounts, motherboard and power supply form factors, wattage, PCIe and 12V-2x6 connectors, M.2 and SATA availability.
- **Power-on simulation.** The case lights up and fans spin, then a BIOS screen walks through the POST. Missing CPU power, no memory, an unpowered graphics card, a dry cooler with no thermal paste, an unplugged power button, an undersized power supply: each fails the way a real PC does, with a debug LED and a fix.
- **Build report.** An overall score out of 100, estimated gaming FPS at 1080p, 1440p and 4K, workload ratings, bottleneck analysis, component ratings, upgrade suggestions and cheaper alternatives.
- **Guided mode.** Fourteen steps in the order a real builder works, each explaining *why* it matters, plus a lesson for every component: what it does, how it connects, specs that matter, buying advice, common mistakes and how to install it. Turn hints off for an unrestricted sandbox.
- **Customisation.** RGB colour and effects, case colour, sleeved cable colours.
- **Saving.** Autosave, named builds, five example builds, and JSON export/import for sharing.
- **Undo and redo** for every action.

## Quick start

Rigsmith is a static site. Because it uses JavaScript modules, browsers need it served over HTTP rather than opened as a file.

```bash
git clone https://github.com/<you>/rigsmith.git
cd rigsmith
npm start            # runs: python3 -m http.server 8080
```

Then open <http://localhost:8080>. Any static server works, for example `npx serve .`.

### Single-file version

```bash
npm install
npm run build:html   # writes dist/rigsmith.html
```

`dist/rigsmith.html` is fully self-contained (about 1 MB, Three.js included) and opens by double-clicking, with no server or internet connection. It's a good file to attach to a GitHub release.

### Deploy to GitHub Pages

Push the repository, then in **Settings › Pages** choose *Deploy from a branch*, select `main` and the root folder. There is nothing to build; Three.js is vendored in `vendor/three`.

### Useful URL options

| Option | Effect |
| --- | --- |
| `?preset=flagship-4k` | Opens an example build (`budget-1080p`, `sweet-1440p`, `flagship-4k`, `sff-itx`, `creator-intel`) |
| `?fresh` | Ignores the autosave and starts empty |
| `?quality=low` | Forces low graphics quality (no shadows, lower resolution). Quality also drops automatically on slow hardware |
| `?skipintro` | Skips the welcome dialog |

## Controls

| Action | Mouse / keyboard |
| --- | --- |
| Orbit, pan, zoom | Left-drag empty space, right-drag, scroll |
| Install a part | Drag its card onto a green outline, or select it and press **Install** |
| Move or remove a part | Drag it in the 3D view; drop it on the parts list to remove it |
| Plug in a cable | Drag its free end onto a green socket, or press **Plug in** in the Wiring tab |
| Route a cable | Press **C** for cable mode, drag the blue mid-handle onto a grommet |
| Camera views | **1** exterior, **2** interior, **3** motherboard, **4** bench, **5** back, **F** close-up |
| Side panel, cable mode, outlines | **P**, **C**, **H** |
| Rotate a part while dragging | **R** |
| Undo, redo, save | **Ctrl/Cmd+Z**, **Ctrl/Cmd+Shift+Z**, **Ctrl/Cmd+S** |
| Remove selected | **Delete** |

Everything you can do by dragging can also be done with buttons in the Details and Wiring tabs, so the app works with a keyboard alone.

## How it works

```
index.html            app shell
css/app.css           design tokens, layout, components
js/
  data/
    catalog.js        every product as plain data (specs, prices)
    guide.js          lessons and guided-build steps
    presets.js        example builds
  core/               pure logic, no DOM or Three.js; unit tested
    build.js          mounts, install rules, cables, ports, keyed connectors
    state.js          store with undo/redo
    compat.js         compatibility checks and power estimate
    post.js           power-on self test simulation
    evaluate.js       scoring, FPS model, bottlenecks, upgrades, savings
    assemble.js       automatic assembly (examples, tests)
    storage.js        localStorage saves, export/import
  three/
    scene.js          renderer, lights, camera views, adaptive quality
    models.js         procedural 3D models at real-world scale
    rig.js            builds the 3D scene from the build state
    cables.js         spline cables, connector heads, port markers
    interaction.js    drag and drop, snapping, cable dragging, routing
  ui/                 sidebars, panels, dialogs, BIOS screen, results
  main.js             app controller
vendor/three/         Three.js r170 (MIT)
tests/
  unit/               node --test for the core engines
  e2e/                Playwright: builds a whole PC with real mouse drags
```

The build is a small JSON document (parts with mount points, cables with endpoints). Every action goes through the store, which validates it, records undo history and notifies the 3D rig and the UI to re-render. Because the scene is rebuilt from state, undo, redo, loading and examples all use the same path.


## Testing

```bash
npm test                 # unit tests for the core engines (Node 20+)
npm run test:e2e         # end-to-end test in a real browser
```

The end-to-end test needs Playwright (`npm install` then `npx playwright install chromium`), or set `CHROME_PATH` to a local Chrome. It builds a complete PC with real pointer drags, checks refusals, wiring, routing, power-on, results, undo/redo, saving and export, and fails on any console error.

## Accuracy

Specifications come from manufacturer spec sheets and prices are approximate US street prices in USD; both change, so check before buying. Performance numbers are **model-based estimates** from relative performance indices in the catalog, not measured benchmarks, and the app says so wherever it shows them. Bottleneck analysis describes tendencies, not exact percentages.

Product and brand names belong to their owners and are used only to identify real hardware. Rigsmith is not affiliated with any manufacturer.

## Browser support

Current Chrome, Edge, Firefox and Safari with WebGL 2. Desktop is the primary target; the layout adapts to tablets and narrow windows. Respects reduced motion, reduced transparency and increased contrast settings.

## Licence

MIT, see [LICENSE](LICENSE). Three.js is MIT licensed by the three.js authors.
