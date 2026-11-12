# Simple Shooting Target Game - Viva & Lab Guide

This project strictly implements the basic requirements of Question 5:
- **Player Camera**: A standard perspective camera facing the targets.
- **Three Target Spheres**: Normal red spheres placed in 3D space.
- **Gun**: A simple cube textured with a gun image.
- **a) Ray from camera**: Uses `THREE.Raycaster` when the mouse button is clicked.
- **b) Target hit & destroy**: Hides the hit sphere (`visible = false`) and increases score by 10.
- **c) Reset button**: Restores all target spheres and resets the score to 0.
- **d) Hit sound**: Plays a simple beep on target hit using Web Audio API.

---

## Code Breakdown

### 1. Scene, Camera, and Light
- Perspective camera placed at `(0, 1.5, 5)` looking toward `(0, 1.5, 0)`.
- Standard directional light and ambient light.

### 2. Objects
- **Gun**: Single box geometry `BoxGeometry(0.4, 0.3, 1.2)` with `assets/gun.jpg`.
- **Targets**: Three spheres with red material `MeshLambertMaterial({ color: 0xef4444 })`.

### 3. Raycasting & Sound (Points a, b, d)
```javascript
// d) Sound
function playHitSound() {
  const ctx = new (window.AudioContext || window.webkitAudioContext)();
  const osc = ctx.createOscillator();
  osc.connect(ctx.destination);
  osc.start();
  osc.stop(ctx.currentTime + 0.1);
}

// a) & b) Raycasting on click
window.addEventListener('click', e => {
  if (e.target.id === 'reset') return;
  mouse.x = (e.clientX / window.innerWidth) * 2 - 1;
  mouse.y = -(e.clientY / window.innerHeight) * 2 + 1;
  raycaster.setFromCamera(mouse, camera);
  const hits = raycaster.intersectObjects(targets.filter(t => t.visible));

  if (hits.length > 0) {
    hits[0].object.visible = false;
    score += 10;
    document.getElementById('score').innerText = score;
    playHitSound();
  }
});
```

### 4. Reset Button (Point c)
```javascript
document.getElementById('reset').addEventListener('click', () => {
  score = 0;
  targets.forEach(t => t.visible = true);
  document.getElementById('score').innerText = score;
});
```

---

## How to Run
```bash
npx serve .
```
Visit `http://localhost:3000/2-shooting-game/` in your browser.
