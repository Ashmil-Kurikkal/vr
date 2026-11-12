# Controlled Ball Bouncing - Code Explanation & Viva Guide

This project demonstrates a simple bouncing ball simulation where two balls bounce on the ground, with one ball having player-controlled directional movement.

---

## 1. Project Overview

- **Ball 1 (Stationary Bounce)**: A sphere with texture that continuously bounces up and down in its position.
- **Ball 2 (Controlled Direction)**: A sphere with texture that bounces up and down while the player controls its horizontal movement (X and Z axes) using WASD or Arrow Keys.
- **Ground**: A flat plane acting as the floor.

---

## 2. Key Code Sections

### 1. Scene, Camera, and Renderer
- Standard Three.js setup with `THREE.Scene()`, `THREE.PerspectiveCamera(60, aspect, 0.1, 1000)` positioned at `(0, 10, 14)`, and `THREE.WebGLRenderer()`.

### 2. Lighting & Ground
- `THREE.DirectionalLight` and `THREE.AmbientLight` provide illumination.
- A `THREE.PlaneGeometry(24, 24)` floor rotated flat (`rotation.x = -Math.PI / 2`).

### 3. The Two Balls
- **Ball 1**: `THREE.SphereGeometry(0.8, 24, 24)` positioned at `(-3, 0.8, 0)`.
- **Ball 2**: `THREE.SphereGeometry(0.8, 24, 24)` positioned at `(3, 0.8, 0)`.

### 4. Keyboard Controls
- Listens to `keydown` and `keyup` events to track active keys in the `keys` object.

### 5. Game Loop & Bouncing Logic
- **Gravity & Bounce**:
  For each ball, gravity decreases the vertical speed each frame:
  ```javascript
  speedY -= gravity;
  ball.position.y += speedY;
  if (ball.position.y <= 0.8) {
    ball.position.y = 0.8;
    speedY = bounceSpeed; // resets upward velocity
  }
  ```
- **Directional Control**:
  Ball 2 moves horizontally based on user input:
  ```javascript
  if (keys['w'] || keys['arrowup']) ball2.position.z -= moveSpeed;
  if (keys['s'] || keys['arrowdown']) ball2.position.z += moveSpeed;
  if (keys['a'] || keys['arrowleft']) ball2.position.x -= moveSpeed;
  if (keys['d'] || keys['arrowright']) ball2.position.x += moveSpeed;
  ```

---

## 3. How to Run
Run in terminal:
```bash
npx serve .
```
Open `http://localhost:3000/1-controlled-ball-bouncing/` in your browser.
