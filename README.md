# Quantum Optics Lab

> **Stop reading about light. Break it.**

An interactive, in-browser optics and quantum-optics playground. Drag, orbit, and tweak 3D experiments in real time, from simple ray tracing to photon statistics and Bell tests. It needs no install, no build step, and no backend. It runs on any modern phone or desktop browser.

**Live site:** <https://nazat02.github.io/Quantum-Optics-Lab/>
**Open the simulator directly:** <https://nazat02.github.io/Quantum-Optics-Lab/simulation.html>

---

## Table of contents

1. [Features](#features)
2. [Experiments](#experiments)
3. [Controls and UX](#controls-and-ux)
4. [Project structure](#project-structure)
5. [Run locally](#run-locally)
6. [Deploy to GitHub Pages](#deploy-to-github-pages)
7. [Architecture notes](#architecture-notes)
8. [Browser support](#browser-support)
9. [Troubleshooting](#troubleshooting)
10. [Known limitations](#known-limitations)
11. [Roadmap](#roadmap)
12. [Contributing](#contributing)
13. [License](#license)

---

## Features

- **Nine experiment tabs** in one app, with **40 optical systems** (lenses, prisms, mirror rooms, light pipes, a raindrop, a brilliant-cut diamond and more) inside the first tab, switchable from a tab strip, covering geometric optics, polarization, resonators, quantum states, interference, and entanglement.
- **Real-time 3D** rendering with [three.js](https://threejs.org/) (r128), with orbit controls on touch and mouse.
- **Mobile-first controls.** The settings panel collapses when you touch the scene and returns with one tap.
- **Remembers your place.** The last experiment you opened is restored on your next visit.
- **Zero dependencies to install.** Two static HTML files; three.js loads from a CDN.
- **Landing page plus simulator**, linked in both directions.

## Experiments

| # | Tab | What it explores |
|---|-----|------------------|
| 1 | **Optics Lab 3D** | 40 ready-to-run optical systems: biconvex to Fresnel lenses, Dove, wedge and retroreflector prisms, mirror rooms, light pipes with total internal reflection, a rainbow-making raindrop, and a brilliant-cut diamond |
| 2 | **Polarization 3D** | Polarization states and polarizers in 3D (Malus's law territory) |
| 3 | **Ray in room** | A single ray bouncing inside a container (for example a box). Adjust azimuth and elevation, switch ray type, and watch the path build up. In a box every bounce flips one direction component, so the path is a regular lattice |
| 4 | **Mirror room** | First-person walk-through of a mirrored room (hold the walk button to move, drag to look) |
| 5 | **Cavity** | Light trapped between mirrors |
| 6 | **Quantum states** | Quantum states of light and their phase-space (Wigner) representations |
| 7 | **Hong-Ou-Mandel** | Two-photon interference and photon bunching |
| 8 | **Double slit** | Interference from two slits, down to single-photon behavior |
| 9 | **Bell / CHSH** | Correlations that beat the classical bound |

Each experiment has its own control panel and live readouts that explain what the numbers mean as you change them.

## Controls and UX

### Touch (phone / tablet)

| Gesture | Action |
|---------|--------|
| Drag on the scene | Orbit the camera |
| Touch the scene | The controls panel **auto-collapses** so the experiment gets the full screen |
| Tap **Controls** pill | Reopen the panel |
| Switch tabs | Panel reopens automatically |
| Swipe the tab strip | Scroll between experiments; the active tab centers itself |
| Tap **‹ Home** | Back to the landing page |

> The **Mirror room** tab is the exception: its panel stays open on touch, because the walk button lives inside it.

### Desktop

| Key / action | Result |
|--------------|--------|
| `1` – `9` | Jump straight to an experiment |
| `[` / `]` | Previous / next experiment |
| `C` | Show or hide the controls panel |
| Mouse drag | Orbit |
| `Esc` | Close open sheets and overlays |
| Mouse click on scene | Does **not** collapse the panel (that behavior is touch-only) |

## Project structure

```
.
├── index.html        # Landing page (must be named index.html for GitHub Pages)
├── simulation.html   # The simulator: all 9 experiments, self-contained
├── LICENSE           # CC BY 4.0 notice
└── README.md
```

- `index.html` links to `simulation.html` from every "Launch the Lab" button.
- `simulation.html` links back to `./` (the landing page) via the **‹ Home** item in the tab strip.
- Keep both files in the **same folder** so the relative links work.

## Run locally

No build tools are needed. Serve the folder with any static server:

```bash
# Python 3
python3 -m http.server 8000

# or Node
npx serve .
```

Then open <http://localhost:8000>.

Opening the files by double-clicking (`file://`) generally works on desktop. On Android, viewing from the Downloads app (`content://…`) often **breaks links between files**. Use a real server or host it online instead.

## Deploy to GitHub Pages

1. Put `index.html`, `simulation.html`, and `README.md` at the **root** of the repository.
2. Go to **Settings → Pages**.
3. Under **Build and deployment**, choose **Deploy from a branch**, then select branch **main** and folder **/ (root)**.
4. Wait a minute or two, then open `https://<your-username>.github.io/<repo-name>/`.

## Architecture notes

- **Single-file apps.** Each page is plain HTML, CSS, and JavaScript with no framework and no bundler, which keeps hosting trivial.
- **Rendering.** three.js r128 is loaded from `cdnjs.cloudflare.com`. 2D experiments (such as the double slit and Bell tests) draw to separate canvases; 3D experiments use a WebGL canvas per mode, and only the active mode is shown and updated.
- **Mode registry.** Experiments are registered in a single map (`M`) with a display name and a control builder. The tab strip is generated from that map, so adding an experiment means registering one more entry.
- **UI layer ("smart controls").** A small, separate script handles panel collapse, tab-strip centering, keyboard shortcuts, and persistence. Persistence uses `localStorage`, wrapped in `try/catch` so private browsing and blocked storage degrade gracefully.
- **Tab strip stability.** Re-centering is idempotent: it only scrolls when the active tab is actually off-center, and it ignores changes to the scroll-arrow buttons. This prevents the left-right shaking loop that earlier versions had.
- **Responsive layout.** Below 820 px wide the panel is a bottom sheet; at 820 px and above it is a side panel toggled with `C`.

## Browser support

Any current evergreen browser with WebGL: Chrome / Edge, Firefox, Safari (iOS and macOS), and Samsung Internet. I have not tested it exhaustively across devices, so please report any rendering problems.

## Troubleshooting

| Symptom | Likely cause and fix |
|---------|----------------------|
| **404 "File not found"** on the site root | No `index.html` at the repo root. Rename the landing page to `index.html` |
| 404 on a sub-page | File names are case-sensitive and must match the URL exactly (`simulation.html`) |
| Site doesn't update after a push | Wait 1–2 minutes and hard-refresh; check **Settings → Pages** |
| Buttons between pages do nothing on Android | You opened the files from Downloads (`content://`). Host them or use a local server |
| Black / empty 3D view | WebGL disabled or blocked, or the three.js CDN is unreachable. Check your connection and browser settings |
| Tabs jitter sideways | You're running an older copy. Replace `simulation.html` with the latest version |

## Known limitations

- **Needs internet for three.js** on first load, because it comes from a CDN. For fully offline use, download `three.min.js` (r128) and change the `<script src>` in `simulation.html` to the local copy.
- Some experiments are **educational models**, tuned for clarity and interactivity rather than research-grade accuracy. Don't use the numbers as a substitute for a validated physics package.
- Very old or low-power phones may lower the frame rate in the heavier 3D scenes.

## Roadmap

Ideas, not promises:

- [ ] Shareable experiment state in the URL (for example `simulation.html#hom`)
- [ ] Preset experiments and a guided "challenge" mode
- [ ] Offline support with a service worker
- [ ] Export screenshots / data from an experiment
- [ ] Automated visual and unit tests for the physics routines
- [ ] Accessibility pass (screen-reader labels, reduced-motion mode)

## Contributing

1. Fork the repo and create a branch: `git checkout -b feature/my-experiment`.
2. Keep changes self-contained. A new experiment should register in the mode map and clean up after itself when its tab is hidden (stop animation loops and listeners).
3. Test on both a phone-width and a desktop-width viewport.
4. Open a pull request describing what you changed and why.

Bug reports are welcome. Please include your device, browser, the experiment tab, and steps to reproduce.

## License

<a href="https://creativecommons.org/licenses/by/4.0/"><img alt="CC BY 4.0" src="https://licensebuttons.net/l/by/4.0/88x31.png"></a>

**Quantum Optics Lab** by **Md. Shaikhul Hadis Nazat** is licensed under the [Creative Commons Attribution 4.0 International License (CC BY 4.0)](https://creativecommons.org/licenses/by/4.0/).

You are free to:

- **Share:** copy and redistribute the material in any medium or format
- **Adapt:** remix, transform, and build upon the material for any purpose, including commercially

Under these terms:

- **Attribution:** you must give appropriate credit, provide a link to the license, and indicate if changes were made. You may do so in any reasonable manner, but not in any way that suggests the author endorses you or your use.
- **No additional restrictions:** you may not apply legal terms or technological measures that legally restrict others from doing anything the license permits.

**Suggested attribution:**

> "Quantum Optics Lab" by Md. Shaikhul Hadis Nazat, licensed under CC BY 4.0. Source: https://github.com/Nazat02/Quantum-Optics-Lab

See the [`LICENSE`](LICENSE) file for the notice and the link to the full legal code.

### Third-party software

[three.js](https://threejs.org/) (r128) is loaded from a CDN and is licensed separately under the MIT License. It is not covered by the CC BY license above.

---

<sub>Built with plain HTML/CSS/JS and three.js.</sub>
