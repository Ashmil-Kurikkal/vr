# Controlled Ball Bouncing - Code Explanation & Viva Guide

This project implements a 3D bouncing ball simulation with full player control using Three.js.

---

## 1. Project Overview

- **Ball**: Sphere with basketball texture that bounces under gravity and user control.
- **Paddle**: A rectangular box controlled by the player using WASD or Arrow Keys.
- **Bounce Control**: 
  - Keys `1`, `2`, `3` switch between Low, Medium, and High bounce strength.
  - `Spacebar` applies a Super Bounce boost.
  - Moving the paddle changes the bounce angle and scores points.

---

## 2. Key Code Sections

### 1. Scene, Camera, and Renderer
- `THREE.Scene()`: Holds all 3D objects, lights, and background color (`#1a202c`).
- `THREE.PerspectiveCamera(60, aspect, 0.1, 1000)`: Simulates real-world perspective. Positioned at `(0, 14, 16)` looking down at the court.
- `THREE.WebGLRenderer`: Renders the 3D scene onto an HTML5 `<canvas>` element.

### 2. Lighting & Ground
- `THREE.DirectionalLight`: Strong directional light casting brightness on objects.
- `THREE.AmbientLight`: Soft general light so shadows are not completely black.
- `THREE.TextureLoader().load(...)`: Loads textures for the court, paddle, and ball with fallback colors.

### 3. Objects
- **Paddle**: `THREE.BoxGeometry(3.6, 0.4, 2.6)` with paddle texture.
- **Ball**: `THREE.SphereGeometry(0.7, 24, 24)` with ball texture.

### 4. Player Controls
- `window.addEventListener('keydown', ...)`:
  - WASD / Arrow keys update `keys` object for smooth movement.
  - Key `1`: Low bounce (`0.16`).
  - Key `2`: Medium bounce (`0.23`).
  - Key `3`: High bounce (`0.32`).
  - Spacebar: Instant super boost (`bouncePower * 1.5`).

### 5. Physics & Collision Logic
- **Gravity & Velocity**:
  ```javascript
  speedY -= gravity;
  ball.position.y += speedY;
  ball.position.x += speedX;
  ball.position.z += speedZ;
  ```
- **Paddle Collision**:
  Checks if the bottom of the ball hits the top of the paddle within the paddle's width and depth:
  ```javascript
  if (ball.position.y <= paddleTop && hitX && hitZ && speedY < 0) {
    speedY = bouncePower;
    score += 1;
  }
  ```
- **Floor Bounce**: If the ball misses the paddle and hits the ground floor, it bounces weakly so play continues.

---

## 3. How to Run
Run in terminal:
```bash
npx serve .
```
Open `http://localhost:3000/1-controlled-ball-bouncing/` in your browser.

