# Complete Line-by-Line Guide: Three.js Exam Game

This document explains **every single line** of [`index.html`](file:///d:/VR/index.html). It is designed to help you understand the concepts thoroughly, memorize the structure, and answer any viva/exam questions with confidence.

---

## 1. HTML Head & Dependencies (Lines 1 – 33)

```html
1: <!DOCTYPE html>
2: <html lang="en">
3: <head>
4:   <meta charset="UTF-8">
5:   <title>Three.js Exam Game - Lion, Cow, Hyena</title>
```
* **Line 1 (`<!DOCTYPE html>`):** Tells the browser that this document uses HTML5 standards.
* **Line 2 (`<html lang="en">`):** Root tag of the webpage, declaring English as the primary language.
* **Line 3 – 5 (`<head>`, `<meta charset="UTF-8">`, `<title>`):** Head section containing page metadata. `UTF-8` ensures emojis (🦁, 🐮, 🐺, ❤️) render properly, and `<title>` defines the browser tab title.

```html
6:   <style>
7:     body {
8:       margin: 0;
9:       overflow: hidden;
10:       font-family: sans-serif;
11:     }
```
* **Line 7 – 11 (`body` styles):**
  * `margin: 0;`: Removes the default 8px browser margin so the 3D canvas fills the screen edge-to-edge.
  * `overflow: hidden;`: Disables scrollbars so the canvas never causes page scrolling.
  * `font-family: sans-serif;`: Sets a clean, standard font for the overlay text.

```html
12:     #hud {
13:       position: absolute;
14:       top: 15px;
15:       left: 15px;
16:       color: white;
17:       background: rgba(0, 0, 0, 0.75);
18:       padding: 10px 18px;
19:       border-radius: 8px;
20:       font-size: 20px;
21:       font-weight: bold;
22:     }
23:     .legend {
24:       font-size: 13px;
25:       font-weight: normal;
26:       color: #ddd;
27:       margin-top: 6px;
28:     }
29:   </style>
```
* **Line 12 – 22 (`#hud` styles):**
  * `position: absolute; top: 15px; left: 15px;`: Floats the Heads-Up Display box on top of the 3D canvas in the top-left corner.
  * `background: rgba(0, 0, 0, 0.75);`: Semi-transparent black box so the 3D world is still visible behind the text.
  * `padding`, `border-radius`, `font-size`: Adds spacing, rounded corners, and clear text size.
* **Line 23 – 28 (`.legend`):** Styles the smaller helper text inside the HUD explaining the controls and shapes.

```html
30:   <!-- Three.js CDN -->
31:   <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
32: </head>
33: <body>
```
* **Line 31:** Imports the Three.js library (version r128) from Cloudflare CDN. Without this single line, `THREE` is undefined. No npm or installation needed.
* **Line 33 (`<body>`):** Starts the visible document body.

---

## 2. HUD Elements (Lines 34 – 40)

```html
34: 
35:   <!-- Minimal HUD -->
36:   <div id="hud">
37:     🦁 Score: <span id="score">0</span> &nbsp;|&nbsp; ❤️ Life: <span id="life">100</span>
38:     <div class="legend">Controls: WASD or Arrow Keys | 🦁 Box | 🐮 Cylinder | 🐺 Cone</div>
39:   </div>
40: 
```
* **Line 36 – 39 (`<div id="hud">`):**
  * `<span id="score">0</span>`: Target element for JavaScript to update when the player touches the Cow.
  * `<span id="life">100</span>`: Target element for JavaScript to update when the player gets hit by the Hyena.
  * `.legend`: Visual hint showing which geometric shape represents which animal.

---

## 3. Scene, Camera & Renderer (Lines 41 – 55)

```javascript
41:   <script>
42:     // -----------------------------------------------------------------
43:     // 1. SCENE, CAMERA, RENDERER
44:     // -----------------------------------------------------------------
45:     const scene = new THREE.Scene();
46:     scene.background = new THREE.Color(0x1a202c);
```
* **Line 45 (`const scene = new THREE.Scene();`):** Creates the 3D universe. Every light, mesh, and model must be added to this `scene` using `scene.add()`.
* **Line 46 (`scene.background = new THREE.Color(0x1a202c);`):** Sets the background color of the 3D world to dark slate grey.

```javascript
48:     const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 1000);
49:     camera.position.set(0, 14, 16);
50:     camera.lookAt(0, 0, 0);
```
* **Line 48 (`new THREE.PerspectiveCamera(fov, aspect, near, far)`):**
  * `60`: Field of View in vertical degrees.
  * `window.innerWidth / window.innerHeight`: Aspect ratio. Ensures circles look round, not squished.
  * `0.1`: Near clipping plane. Any object closer than 0.1 units is clipped.
  * `1000`: Far clipping plane. Any object beyond 1000 units is not drawn.
* **Line 49 (`camera.position.set(0, 14, 16);`):** Positions the camera:
  * `X = 0`: Centered horizontally.
  * `Y = 14`: Elevated 14 units up in the air.
  * `Z = 16`: Pulled back 16 units towards the viewer.
* **Line 50 (`camera.lookAt(0, 0, 0);`):** Angles the camera downward to aim at the origin `(0, 0, 0)` of the arena.

```javascript
52:     const renderer = new THREE.WebGLRenderer({ antialias: true });
53:     renderer.setSize(window.innerWidth, window.innerHeight);
54:     document.body.appendChild(renderer.domElement);
```
* **Line 52 (`new THREE.WebGLRenderer({ antialias: true })`):** Initializes the WebGL engine that talks to your GPU. `antialias: true` smooths jagged edges.
* **Line 53 (`renderer.setSize(...)`):** Resizes the internal canvas buffer to match the full browser window.
* **Line 54 (`document.body.appendChild(renderer.domElement)`):** Adds the generated `<canvas>` element directly into the HTML body.

---

## 4. Lighting & Ground Plane (Lines 56 – 69)

```javascript
56:     // -----------------------------------------------------------------
57:     // 2. LIGHTING & GROUND
58:     // -----------------------------------------------------------------
59:     const light = new THREE.DirectionalLight(0xffffff, 1.2);
60:     light.position.set(5, 15, 8);
61:     scene.add(light, new THREE.AmbientLight(0x555555));
```
* **Line 59 (`new THREE.DirectionalLight(0xffffff, 1.2)`):** Simulates sunlight. It shines parallel white rays with an intensity of 1.2.
* **Line 60 (`light.position.set(5, 15, 8)`):** Sets where the sun shines from (upper right and forward).
* **Line 61 (`scene.add(light, new THREE.AmbientLight(0x555555))`):**
  * Adds the directional light.
  * Also adds an `AmbientLight` with soft grey color `0x555555`. Ambient light illuminates all sides of every object equally so the back faces are never pure black.

```javascript
63:     const ground = new THREE.Mesh(
64:       new THREE.PlaneGeometry(24, 24),
65:       new THREE.MeshLambertMaterial({ color: 0x2d3748 })
66:     );
67:     ground.rotation.x = -Math.PI / 2;
68:     scene.add(ground);
```
* **Line 63 – 66 (`ground = new THREE.Mesh(...)`):**
  * `PlaneGeometry(24, 24)`: A flat square surface 24 units wide by 24 units tall.
  * `MeshLambertMaterial({ color: 0x2d3748 })`: A matte, non-shiny material that reacts realistically to lights.
* **Line 67 (`ground.rotation.x = -Math.PI / 2;`):**
  * **Crucial Exam Concept:** By default, Three.js planes are vertical (facing along the Z axis like a wall). Rotating it by `-90 degrees` (`-π/2 radians`) around the X-axis lays it flat on the XZ plane like a floor.
* **Line 68 (`scene.add(ground)`):** Places the floor inside the 3D scene.

---

## 5. The 3 Objects: Abstract Shapes + Textures (Lines 70 – 106)

```javascript
70:     // -----------------------------------------------------------------
71:     // 3. THE 3 OBJECTS (ABSTRACT SHAPES + IMAGES / COLORS)
72:     // -----------------------------------------------------------------
73:     const loader = new THREE.TextureLoader();
74: 
75:     // Helper to create material with image texture and fallback color
76:     function makeMat(imagePath, color) {
77:       return new THREE.MeshLambertMaterial({
78:         map: loader.load(imagePath),
79:         color: color
80:       });
81:     }
```
* **Line 73 (`const loader = new THREE.TextureLoader();`):** Utility to load 2D images as WebGL textures.
* **Line 76 – 81 (`makeMat(imagePath, color)`):** Helper function to keep code clean and short:
  * `map: loader.load(imagePath)`: Loads the animal face/avatar texture onto the mesh surface.
  * `color: color`: Base tint color. If an image path is missing or blocked by local browser permissions, the object still displays this clear signature color.

```javascript
83:     // 1) Lion (Player) - Golden Box
84:     const lion = new THREE.Mesh(
85:       new THREE.BoxGeometry(1.5, 1.5, 1.5),
86:       makeMat('assets/lion.jpg', 0xf59e0b)
87:     );
88:     lion.position.set(0, 0.75, 4);
89:     scene.add(lion);
```
* **Line 84 – 86:**
  * **Shape:** `BoxGeometry(1.5, 1.5, 1.5)` — A Cube (1.5 units on each side).
  * **Texture/Color:** Lion avatar image with amber/gold tint `0xf59e0b`.
* **Line 88 (`lion.position.set(0, 0.75, 4);`):** Places the lion at `X=0`, `Z=4`. Because the box height is 1.5, setting `Y=0.75` places the bottom of the cube exactly touching the ground (`0.75 = 1.5 / 2`).
* **Line 89:** Adds the lion to the scene.

```javascript
91:     // 2) Cow (Prey) - White Cylinder
92:     const cow = new THREE.Mesh(
93:       new THREE.CylinderGeometry(0.7, 0.7, 1.3, 16),
94:       makeMat('assets/cow.jpg', 0xffffff)
95:     );
96:     cow.position.set(-5, 0.65, -3);
97:     scene.add(cow);
```
* **Line 92 – 95:**
  * **Shape:** `CylinderGeometry(radiusTop, radiusBottom, height, segments)`. A smooth cylinder with radius 0.7 and height 1.3.
  * **Texture/Color:** Cow avatar image with white tint `0xffffff`.
* **Line 96 (`cow.position.set(-5, 0.65, -3);`):** Places the cow to the left (`X=-5`) and slightly forward (`Z=-3`). Height 1.3 / 2 = 0.65 so it sits on the ground.
* **Line 97:** Adds the cow to the scene.

```javascript
99:     // 3) Hyena (Threat) - Red Cone
100:     const hyena = new THREE.Mesh(
101:       new THREE.ConeGeometry(0.8, 1.8, 8),
102:       makeMat('assets/hyena.jpg', 0xef4444)
103:     );
104:     hyena.position.set(5, 0.9, -4);
105:     scene.add(hyena);
```
* **Line 100 – 103:**
  * **Shape:** `ConeGeometry(radius, height, segments)`. An aggressive pointed cone/pyramid.
  * **Texture/Color:** Hyena avatar image with crimson red tint `0xef4444`.
* **Line 104 (`hyena.position.set(5, 0.9, -4);`):** Places the hyena to the right (`X=5`) and forward (`Z=-4`). Height 1.8 / 2 = 0.9.
* **Line 105:** Adds the hyena to the scene.

---

## 6. Keyboard Input System (Lines 107 – 113)

```javascript
107:     // -----------------------------------------------------------------
108:     // 4. KEYBOARD CONTROLS (LION MOVEMENT)
109:     // -----------------------------------------------------------------
110:     const keys = {};
111:     window.addEventListener('keydown', e => { keys[e.key.toLowerCase()] = true; });
112:     window.addEventListener('keyup', e => { keys[e.key.toLowerCase()] = false; });
```
* **Line 110 (`const keys = {};`):** An empty object that acts as a lookup dictionary of currently pressed keys (e.g. `{ 'w': true, 'arrowup': true }`).
* **Line 111 (`keydown`):** When any key is pressed down, sets `keys[keyName] = true`. `.toLowerCase()` guarantees `W` and `w` work the same regardless of CapsLock.
* **Line 112 (`keyup`):** When the key is released, sets `keys[keyName] = false`.
* **Why this is exam-standard:** If you move an object directly inside `keydown`, the movement lags due to the operating system's keyboard repeat delay (tap ... wait ... tap-tap-tap). Storing booleans allows the animation loop to move smoothly every frame.

---

## 7. Game State & Animation Loop (Lines 114 – 168)

```javascript
114:     // -----------------------------------------------------------------
115:     // 5. GAME LOOP & INTERSECTIONS
116:     // -----------------------------------------------------------------
117:     let score = 0;
118:     let life = 100;
119:     let lastHitTime = 0;
```
* **Line 117 (`let score = 0;`):** Player score, starts at 0.
* **Line 118 (`let life = 100;`):** Player health, starts at 100.
* **Line 119 (`let lastHitTime = 0;`):** Timestamp tracker for cooldowns if needed.

```javascript
121:     function animate() {
122:       requestAnimationFrame(animate);
123: 
124:       if (life > 0) {
```
* **Line 121 (`function animate()`):** The main game loop function executed on every single frame (~60 times per second).
* **Line 122 (`requestAnimationFrame(animate);`):** Requests the browser to call `animate` again before the next screen repaint. Ensures silky 60 FPS and pauses automatically if the user switches tabs to save battery.
* **Line 124 (`if (life > 0)`):** Checks if the player is still alive. If life reaches 0, all movement and AI stops.

### A. Lion Movement (WASD & Arrows)
```javascript
125:         // Lion Movement
126:         const speed = 0.15;
127:         if (keys['w'] || keys['arrowup']) lion.position.z -= speed;
128:         if (keys['s'] || keys['arrowdown']) lion.position.z += speed;
129:         if (keys['a'] || keys['arrowleft']) lion.position.x -= speed;
130:         if (keys['d'] || keys['arrowright']) lion.position.x += speed;
```
* **Line 126 (`const speed = 0.15;`):** Distance the lion travels per frame.
* **Line 127 (`w` / `arrowup`):** In Three.js, negative Z is **forward** (into the screen). Decreasing `lion.position.z` moves the lion forward.
* **Line 128 (`s` / `arrowdown`):** Positive Z is **backward** (towards the camera).
* **Line 129 (`a` / `arrowleft`):** Negative X is **left**.
* **Line 130 (`d` / `arrowright`):** Positive X is **right**.
*(Holding W + D automatically moves diagonally at the same time!)*

### B. Chasing Logic (Hyena pursues Lion)
```javascript
132:         // Chasing Logic: Hyena chases Lion
133:         const hyenaSpeed = 0.04;
134:         if (hyena.position.x < lion.position.x) hyena.position.x += hyenaSpeed;
135:         if (hyena.position.x > lion.position.x) hyena.position.x -= hyenaSpeed;
136:         if (hyena.position.z < lion.position.z) hyena.position.z += hyenaSpeed;
137:         if (hyena.position.z > lion.position.z) hyena.position.z -= hyenaSpeed;
```
* **Line 133 (`const hyenaSpeed = 0.04;`):** Hyena moves slower than the player (`0.04 < 0.15`) so the player can outrun it.
* **Line 134 – 137:** The chasing AI algorithm:
  * If the hyena is to the left of the lion (`hyena.x < lion.x`), move right (`+`).
  * If the hyena is to the right of the lion (`hyena.x > lion.x`), move left (`-`).
  * If the hyena is behind the lion (`hyena.z < lion.z`), move forward (`+`).
  * If the hyena is ahead of the lion (`hyena.z > lion.z`), move backward (`-`).
* *Result:* The hyena actively stalks and tracks the player everywhere they move!

### C. Cow Idle Animation
```javascript
139:         // Cow gentle rotation
140:         cow.rotation.y += 0.02;
```
* **Line 140:** Slowly rotates the cow around its vertical Y-axis to make it look alive and dynamic.

### D. Intersection 1: Lion & Cow (Score Increment)
```javascript
142:         // --- INTERSECTION 1: Lion & Cow (Score Increments) ---
143:         if (lion.position.distanceTo(cow.position) < 1.5) {
144:           score += 10;
145:           document.getElementById('score').innerText = score;
146:           // Respawn cow at a new random position
147:           cow.position.x = (Math.random() - 0.5) * 14;
148:           cow.position.z = (Math.random() - 0.5) * 14;
149:         }
```
* **Line 143 (`lion.position.distanceTo(cow.position) < 1.5`):**
  * **Exam Keyword:** `distanceTo()` calculates the 3D Euclidean distance: $\sqrt{(x_2-x_1)^2 + (y_2-y_1)^2 + (z_2-z_1)^2}$.
  * If distance is less than `1.5`, a collision/intersection has occurred!
* **Line 144:** Increments `score` by 10 points.
* **Line 145:** Updates the HTML element `<span id="score">` so the player sees the new score.
* **Line 147 – 148:** Teleports the cow to a new random X and Z coordinate within the arena boundaries (`-7` to `+7`), ready to be caught again!

### E. Intersection 2: Lion & Hyena (Life Decrement)
```javascript
151:         // --- INTERSECTION 2: Lion & Hyena (Life Decreases) ---
152:         if (lion.position.distanceTo(hyena.position) < 1.5) {
153:           life = Math.max(0, life - 20);
154:           document.getElementById('life').innerText = life;
155:           // Respawn Hyena at a distance so it chases again
156:           hyena.position.set(8, 0.9, -8);
157: 
158:           if (life === 0) {
159:             document.getElementById('hud').innerHTML += ' <span style="color:#ef4444"> — GAME OVER!</span>';
160:           }
161:         }
162:       }
```
* **Line 152:** Checks if the hyena reaches the lion (distance < 1.5).
* **Line 153 (`life = Math.max(0, life - 20);`):** Deducts 20 health points. `Math.max(0, ...)` prevents health from displaying negative numbers like `-20`.
* **Line 154:** Updates the HTML element `<span id="life">`.
* **Line 156 (`hyena.position.set(8, 0.9, -8);`):** Resets the hyena back to its corner so it doesn't instantly drain all health in one spot, and gives the player time to run while the hyena resumes its chase!
* **Line 158 – 160:** If life reaches 0, appends "GAME OVER!" in bold red to the top HUD.

### F. Rendering the Frame
```javascript
164:       renderer.render(scene, camera);
165:     }
166: 
167:     animate();
```
* **Line 164 (`renderer.render(scene, camera);`):** Takes a snapshot of the `scene` through the viewpoint of the `camera` and paints it onto the screen. Must be called every frame.
* **Line 167 (`animate();`):** Starts the loop for the very first frame.

---

## 8. Window Resize Handler (Lines 169 – 178)

```javascript
169:     // Window Resize
170:     window.addEventListener('resize', () => {
171:       camera.aspect = window.innerWidth / window.innerHeight;
172:       camera.updateProjectionMatrix();
173:       renderer.setSize(window.innerWidth, window.innerHeight);
174:     });
175:   </script>
176: </body>
177: </html>
```
* **Line 170 (`window.addEventListener('resize', ...)`):** Fires whenever the browser window is resized or maximized.
* **Line 171 (`camera.aspect = ...`):** Recalculates the camera width-to-height ratio.
* **Line 172 (`camera.updateProjectionMatrix();`):** Tells Three.js to apply the new aspect ratio to the camera's internal projection matrix (without this, the 3D scene stretches).
* **Line 173 (`renderer.setSize(...)`):** Resizes the WebGL drawing canvas to the new dimensions.
* **Line 175 – 177:** Closes the `<script>`, `<body>`, and `<html>` tags.

---

## 🎯 Summary of Common Exam / Viva Questions

| Question | Answer |
| :--- | :--- |
| **Why use `requestAnimationFrame` instead of `setInterval`?** | `requestAnimationFrame` synchronizes with the monitor's refresh rate (60Hz/144Hz), avoids screen tearing, and automatically pauses when the browser tab is minimized to save GPU/battery. |
| **Why `MeshLambertMaterial` instead of `MeshBasicMaterial`?** | `MeshBasicMaterial` ignores all lights and appears completely flat with no shading. `MeshLambertMaterial` calculates diffuse reflections from lights, giving 3D depth and realism. |
| **Why `ground.rotation.x = -Math.PI / 2`?** | Planes in Three.js are oriented vertically on the XY plane by default. Rotating -90 degrees (-π/2 radians) flips it onto the horizontal XZ plane. |
| **How does intersection detection work?** | We use `lion.position.distanceTo(target.position) < 1.5`, which calculates the 3D distance between the center points of the two meshes. |
| **What are the coordinates of the 3 axes in Three.js?** | `X` = Left (-) / Right (+)<br>`Y` = Down (-) / Up (+)<br>`Z` = Forward into screen (-) / Backward toward camera (+) |
