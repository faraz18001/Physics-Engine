# Project: "Void Engine" — Custom C++ 3D Physics Simulation

**Goal:** Build a 3D gravitational physics engine from scratch without using OpenGL/DirectX matrices, utilizing SFML only for the final 2D rasterization.

---

## Phase 1: The Mathematical Foundation (The `Vec3` Class)

**Time Estimate:** 1-2 Days

Before you can render a single pixel, you need a mathematical object to represent 3D space.  
You cannot use SFML's `sf::Vector2f` for this.

### Key Objectives

- **Create a class/struct `Vec3`.**
- **Implement operator overloading for elegant syntax:**
  - `+`, `-`, `*` (scalar multiplication), `/`
  - `+=`, `-=`
- **Implement vector math functions:**
  - `magnitude()`: Returns the length of the vector:  
    \[
    \sqrt{x^2 + y^2 + z^2}
    \]
  - `normalize()`: Returns a vector of length 1 (essential for gravity direction).
  - `dot(Vec3)`: Dot product.
  - `cross(Vec3)`: Cross product (Essential for calculating orbital velocity vectors).

---

## Phase 2: The "Camera" & Projection Pipeline

**Time Estimate:** 2 Days

This is the engine core. You must write the function that takes a star at `(100, 50, 500)` and tells SFML where to put it on your 2D screen.

### The Projection Formula (Perspective)

\[
x_{screen} = \frac{x_{world} \times \text{focal\_length}}{z_{world} + \text{camera\_offset}} + \text{screen\_center\_x}
\]

\[
y_{screen} = \frac{y_{world} \times \text{focal\_length}}{z_{world} + \text{camera\_offset}} + \text{screen\_center\_y}
\]

### Key Objectives

- Define a `Camera` struct with a position `(x, y, z)`.
- Create a function `project(Vec3 point3D)` that returns an `sf::Vector2f`.
- **Debug Test:** Place a single static point at `(0, 0, 100)` and print its 2D projected coordinates to the console.

---

## Phase 3: The Physics System (Newtonian Gravity)

**Time Estimate:** 2-3 Days

Simulate an accretion disk (a black hole with matter swirling around it).

### Key Objectives

- **Particle Struct:** Needs `Vec3 position`, `Vec3 velocity`, `sf::Color color`.
- **Initialization:**
  - Spawn 2,000 particles.
  - Position them in a random ring around `(0, 0, 0)`.
- **Crucial Math:**  
  Give them an initial velocity tangent to the center so they orbit instead of falling in immediately:
  -  
    \[
    \text{Velocity} = \text{CrossProduct}(\text{Position}, \text{UpVector})
    \]
- **The Update Loop:**
  - Calculate vector from particle to center.
  - Apply gravity:  
    \[
    F = \frac{G \cdot M}{r^2}
    \]
  - Update velocity:  
    `velocity += force`
  - Update position:  
    `position += velocity`

---

## Phase 4: Rendering & Optimization (Vertex Arrays)

**Time Estimate:** 2 Days

If you draw 2,000 separate `sf::CircleShape` objects, your integrated graphics will choke.  
You must use a vertex array.

### Key Objectives

- **Vertex Array:** Use `sf::VertexArray` with type `sf::Points`.
- **The "Z-Sorting" Problem (Depth):**
  - In 3D, things far away must be drawn before things close to the camera.
  - **Algorithm:** Sort your list of particles based on their z coordinate every frame ("Painter's Algorithm").
- **Visual Polish:**
  - Map opacity (alpha) to z depth (stars fade out as they get further away).
  - Map color to velocity (fast stars = blue, slow stars = red).

---

## Phase 5: Interaction (Rotation Matrices)

**Time Estimate:** 2 Days

The simulation is boring if the camera is stuck.  
We need to rotate the "world" around the camera to simulate looking around.

### Key Objectives

- **Implement a rotation matrix function for Y-axis rotation:**
  \[
  x_{new} = x \cdot \cos(\theta) - z \cdot \sin(\theta)
  \]
  \[
  z_{new} = x \cdot \sin(\theta) + z \cdot \cos(\theta)
  \]
- Bind arrow keys to increase/decrease the angle \(\theta\).
- Apply this rotation to every particle before the projection step.

---
