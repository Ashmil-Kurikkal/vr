# 1v1 Soccer Game - Code Explanation & Viva Guide

This project implements a 1-player-a-side 3D soccer game in Three.js.

---

## 1. Project Overview

- **Player 1 (User / Blue)**: A cube textured with Player 1's face, controlled with WASD or Arrow Keys.
- **Player 2 (AI Opponent / Red)**: A cube textured with Player 2's face, automatically chasing the soccer ball.
- **Soccer Ball**: A sphere with classic soccer ball texture that rolls and bounces with physics and friction.
- **Pitch & Goals**: Green grass pitch with boundary markings and two 3D goalposts.

---

## 2. Key Code Sections

### 1. Scene, Camera & Lighting
- `THREE.PerspectiveCamera`: Positioned high at `(0, 16, 17)` looking down at the pitch at `(0, 0, 0)` for a clear stadium broadcast angle.
- `THREE.DirectionalLight` + `THREE.AmbientLight` provide clean lighting across the field.

### 2. Player Textures on Cubes
- Each player is modeled as a `THREE.BoxGeometry(1.4, 1.4, 1.4)`.
- Player 1 has `assets/player1.jpg` (portrait face with blue jersey).
- Player 2 has `assets/player2.jpg` (portrait face with red jersey).
- Fallback colors (`0x2563eb` and `0xdc2626`) ensure the cubes stay clearly recognizable if textures are loading.

### 3. Player Movement & AI
- **Player 1**: Moves in X and Z axes based on `keys['w']`, `keys['s']`, `keys['a']`, `keys['d']` or Arrow Keys. Clamped within the field lines.
- **Player 2 (AI)**:
  Uses clear, simple tracking logic:
  ```javascript
  if (player2.position.x < ball.position.x) player2.position.x += aiSpeed;
  if (player2.position.x > ball.position.x) player2.position.x -= aiSpeed;
  if (player2.position.z < ball.position.z) player2.position.z += aiSpeed;
  if (player2.position.z > ball.position.z) player2.position.z -= aiSpeed;
  ```

### 4. Ball Physics & Kicking
- **Movement & Friction**:
  ```javascript
  ball.position.x += ballVx;
  ball.position.z += ballVz;
  ballVx *= 0.96; // 4% friction per frame
  ballVz *= 0.96;
  ```
- **Kicking Intersection**:
  When a player touches the ball (`distanceTo < 1.35`), the ball is given velocity proportional to the vector from the player to the ball, plus forward momentum toward the opponent's goal.
- **Touchline Bounces**:
  If the ball hits the side lines (`Math.abs(ball.position.x) > 7`), it bounces back into play (`ballVx = -ballVx`).

### 5. Goal Detection & Reset
- **Top Goal (`z < -12.2` and `|x| < 2.5`)**: Player 1 scores! Score increments, ball resets to center `(0, 0.65, 0)` with zero velocity.
- **Bottom Goal (`z > 12.2` and `|x| < 2.5`)**: Player 2 scores!

---

## 3. How to Run
Run in terminal:
```bash
npx serve .
```
Open `http://localhost:3000/3-soccer-game/` in your browser.

