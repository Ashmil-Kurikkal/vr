# 1v1 Soccer Game - Code Explanation & Viva Guide

This project implements a 1-player-a-side 3D soccer game in Three.js with solid collisions and kick-off logic.

---

## 1. Project Overview

- **Player 1 (User / Blue)**: A cube textured with Player 1's face, controlled with WASD or Arrow Keys.
- **Player 2 (CPU / Red)**: A cube textured with Player 2's face, defending its half and chasing the ball.
- **Soccer Ball**: Plain white sphere with physics and wall reflection.
- **Pitch**: Plain green surface with two white 3D goalposts.
- **Kick-Off**: Player 1 starts with the ball; the CPU waits until Player 1 kicks off.
- **Player Collision**: The two player cubes physically collide and push off each other instead of phasing through.

---

## 2. Key Code Sections

### 1. Scene, Camera, and Green Pitch
- Plain green `THREE.PlaneGeometry(16, 26)` with `color: 0x2e7d32`.
- Plain white ball `THREE.SphereGeometry(0.65, 24, 24)` with `color: 0xffffff`.
- Player 1 and Player 2 cubes mapped with face images (`player1.jpg` and `player2.jpg`).

### 2. Player-to-Player Collision
When the two player cubes meet (`distanceTo < 1.4`), an overlap push vector is applied so they push each other away solidly:
```javascript
const pDist = player1.position.distanceTo(player2.position);
if (pDist < 1.4 && pDist > 0.001) {
  const overlap = (1.4 - pDist) / 2;
  const dx = (player1.position.x - player2.position.x) / pDist;
  const dz = (player1.position.z - player2.position.z) / pDist;
  player1.position.x += dx * overlap;
  player1.position.z += dz * overlap;
  player2.position.x -= dx * overlap;
  player2.position.z -= dz * overlap;
}
```

### 3. Kick-Off & AI
- At start and after every goal, `isKickOff = true`. The ball is placed at center `(0, 0.65, 0)`, Player 1 at `(0, 0.7, 4)`, and CPU waits back at `(0, 0.7, -6)`.
- When Player 1 touches the ball, `isKickOff = false` and play begins.
- The CPU aims for `ball.position.z - 1.2` (behind the ball) to kick forward, and holds back to defend if the ball is deep in Player 1's territory.

### 4. Wall Bounces & Corners
Wall collisions push the ball into the field and apply negative velocity so the ball and players never get trapped or stuck in corners:
```javascript
if (ball.position.x > 6.8) {
  ball.position.x = 6.7;
  ballVx = -Math.abs(ballVx) * 0.8 - 0.04;
}
```

---

## 3. How to Run
Run in terminal:
```bash
npx serve .
```
Open `http://localhost:3000/3-soccer-game/` in your browser.
