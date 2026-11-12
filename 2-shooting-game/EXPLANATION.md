# Simple Shooting Target Game - Code Explanation & Viva Guide

This project implements a 3D first-person shooting game answering all parts of the lab specification:
- a) Shoot a ray from the centre of the camera when the mouse button is clicked.
- b) When a target is hit, destroy it and increase the score.
- c) Add a reset button to restore all targets and the score.
- d) Play a hit sound when a target is successfully hit.

---

## 1. Project Overview

- **Camera**: First-person perspective camera that can be aimed using the mouse.
- **Gun**: 3D gun object (box geometry with gun texture + barrel) attached directly to the camera with a recoil animation on firing.
- **Targets**: Three target spheres with target texture floating in 3D space.
- **Crosshair**: Centered `+` crosshair on screen.

---

## 2. Step-by-Step Viva Explanation

### a) Raycasting from the Centre of the Camera
- A `THREE.Raycaster` is used.
- The screen centre coordinate in Normalized Device Coordinates (NDC) is `(0, 0)`.
- `raycaster.setFromCamera(new THREE.Vector2(0, 0), camera)` casts an invisible ray forward from the camera's optical centre directly through the crosshair.

### b) Target Hit Detection & Destruction
- `const hits = raycaster.intersectObjects(targets.filter(t => t.visible));`
- If an intersection is found:
  - `hitTarget.visible = false;` (destroys/hides the target).
  - `score += 10;` (updates score display).
  - Triggers the hit sound.

### c) Reset Button
- An HTML button `<button id="reset-btn">Reset</button>` is styled in the HUD.
- When clicked:
  ```javascript
  score = 0;
  targets.forEach(t => t.visible = true);
  ```
  Restores all 3 targets and resets the score counter to 0.

### d) Sound on Target Hit
- Uses the browser's built-in **Web Audio API** (`AudioContext`).
- Creates an oscillator node playing a tone that sweeps downward:
  ```javascript
  const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
  const osc = audioCtx.createOscillator();
  osc.frequency.setValueAtTime(600, audioCtx.currentTime);
  osc.frequency.exponentialRampToValueAtTime(140, audioCtx.currentTime + 0.14);
  ```
- Self-contained: requires no external mp3/wav files and never fails to load.

---

## 3. How to Run
Run in terminal:
```bash
npx serve .
```
Open `http://localhost:3000/2-shooting-game/` in your browser.

