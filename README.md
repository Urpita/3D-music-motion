<p align="center">
  <img src="galaxy.svg" alt="Rotating spiral galaxy of glowing stars" width="100%">
</p>

<h1 align="center">Mood Galaxy</h1>

<p align="center">
  A music-reactive particle galaxy that runs entirely in the browser.<br>
  About 45,000 GPU-driven stars that react to sound, your cursor and the mood you pick.
</p>

<p align="center">
  <a href="https://serene-dodol-3ad7e3.netlify.app/"><b>Live demo</b></a>
</p>

---

## Features

- **Four moods:** Chill, Focus, Workout and Night. Each one smoothly changes the galaxy's spiral tightness, spread, rotation speed and colour tint, and the generated music's tempo, beat pattern and chords.
- **Music reactivity:** bass makes the core pulse and the whole galaxy breathe, mids brighten the stars and speed up the rotation, and highs make the stars sparkle.
- **Beat shockwaves:** a sudden jump in bass fires a ring that ripples outward through the arms.
- **Hover float:** particles near your cursor drift away and float, then settle back into their arms.
- **Click and hold:** a quick click sends a shockwave from the cursor. Holding the pointer down pulls nearby particles in.
- **Shape morphing:** turn the galaxy into a Ring, a Sphere or a Heart, and back again.
- **Three audio sources:** music generated live in the browser, your own audio file, or your microphone.
- **Bloom:** optional post-processing glow, with a toggle.
- **Adaptive quality:** if the frame rate drops, the page lowers its resolution first and then switches bloom off.
- **Accessible motion:** rotation stops for visitors who prefer reduced motion.

## Controls

| Action | What it does |
| --- | --- |
| Move the pointer over the galaxy | Nearby particles float away |
| Click | Shockwave from the cursor |
| Press and hold | Pulls nearby particles toward the cursor |
| Mood buttons | Reshape and recolour the galaxy, and change the music |
| Shape buttons | Morph into a ring, sphere or heart |
| Play / Pause | Start or stop the generated music (or your loaded track) |
| Load your track | Use any audio file from your device |
| Mic | React to live sound from your microphone |
| Bloom | Turn the glow effect on or off |

## How it works

| Layer | Technology |
| --- | --- |
| Rendering | WebGL through [Three.js](https://threejs.org/) r128, with every star drawn as one point in a single `THREE.Points` draw call |
| Motion | Custom GLSL vertex shader: orbit, hover float, shockwave ring, shape morph and twinkle are all computed on the GPU |
| Glow | Fragment shader (soft circular sprite) with additive blending, plus `UnrealBloomPass` post-processing |
| Audio | Web Audio API: oscillators and noise for the generated music, an `AnalyserNode` for frequency data |
| Reactivity | The spectrum is split into bass, mid and high bands, smoothed (fast attack, slow release) and sent to the shader as uniforms |
| Smoothness | Exponential easing for the cursor and mood changes, and an accumulated rotation phase so speed changes never cause jumps |

A few details worth knowing:

- **Spiral:** each star sits on a logarithmic spiral arm with Gaussian scatter. Inner stars orbit faster than outer ones, so the arms slowly reshape.
- **Hover:** the pointer is converted onto the galaxy's plane and passed to the shader as `uMouse`. A `smoothstep` falloff makes the effect fade in and out instead of switching on.
- **Shape morph:** every particle has a second, static target position. The galaxy fades back to its spiral, the target shape is swapped in, and the particles then fade into it.
- **Mood data:** moods are plain objects in the `MOODS` table at the top of the script, so adding one takes a single line.

## Run it locally

The whole project is one file, so there is nothing to install.

```bash
git clone https://github.com/Urpita/mood-galaxy.git
cd mood-galaxy
npx serve .
```

Then open the address it prints. You can also open `index.html` directly, but a local server is better because the microphone only works on `https://` pages or on `localhost`.

## Deploy on GitHub Pages

1. Push `index.html` to the `main` branch.
2. Open **Settings → Pages**.
3. Set the source to the `main` branch and save.
4. After a minute or two, the site is live at `https://<your-username>.github.io/mood-galaxy/`.

## Customise

| What | Where |
| --- | --- |
| Star count | `N` (and `CLUMPS`, `CORE`) near the top of the script |
| Galaxy size | `R` |
| Moods | The `MOODS` table: `speed`, `twist`, `spread`, `tint`, `bpm`, `kick`, `hat`, `chords` |
| Hover area | `uRadius` in the uniforms |
| Hover push distance and wobble | The `mag` and `wob` lines in the vertex shader |
| Bloom strength | The three numbers passed to `UnrealBloomPass` |
| Shapes | `fillShape()` and the button list that sets `shapeWant` |

## Browser support and notes

- Works in current Chrome, Edge, Firefox and Safari with WebGL enabled.
- Browsers only allow audio after a click, so nothing plays until you press Play, load a track or turn on the microphone.
- The microphone is analysed only. It is never played back through the speakers, so there's no feedback.
- Music from streaming services can't be analysed, because the browser doesn't give a page access to that audio. Use a local file or the microphone.
- On phones the page uses fewer stars, and the adaptive quality step lowers the resolution further if needed.
- Three.js and the bloom scripts load from public CDNs, so the page needs an internet connection the first time.

## Roadmap

- Stateful particles with a GPU simulation, so hover leaves real trails
- Scroll-driven camera and mood changes
- A Next.js and React Three Fiber version with a reusable `<MoodGalaxy />` component

## Related

[`assets/galaxy.svg`](assets/galaxy.svg) is a pure-SVG animated version of the galaxy, made for READMEs where JavaScript is not allowed.

## License

MIT. Add a `LICENSE` file to the repo to make it official.
