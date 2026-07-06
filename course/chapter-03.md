# Chapter 3 — Cameras, Projections, and Camera Control

A 3D scene has no meaning without a viewpoint. In the previous chapter, we made objects move, rotate, and scale. In this chapter, we will focus on the camera, which decides what part of the world you see. Cameras are among the most important artistic and technical tools in a 3D application because they shape the user’s experience of the world.

We will continue modifying the same project file in [course/project.html](course/project.html). The project will still stay in one HTML file while growing in sophistication.

## 3.1 What a camera is

### What it is

A camera is a virtual viewpoint that observes the scene. It determines what is visible and how the world appears on the screen.

### Why it exists

The GPU cannot render a scene without a perspective or projection model. The camera defines that model.

### How it works internally

A camera creates a view matrix and, for perspective cameras, a projection matrix. Those matrices transform world-space coordinates into camera-space and then into clip space.

### When to use it

Use a camera whenever you need to look at the scene from a particular point of view.

### Common mistakes

A common beginner mistake is to place an object in front of the camera but forget that the camera direction also matters. An object can be in the right position and still be off-screen.

### Performance considerations

Cameras are cheap, but using several cameras or very large view frustums can hurt performance if the renderer is forced to do unnecessary work.

### Real-world uses

Cameras are used in games, architectural walkthroughs, simulators, filmmaking tools, and web-based tours.

### Step-by-step implementation

We will:

- create a PerspectiveCamera
- position it in the scene
- make it look at the center of the world
- update it during animation

### Beginner explanation

The camera is like your eyes in the 3D world.

### Intermediate explanation

It determines the projection and the viewing direction.

### Advanced explanation

The camera defines a frustum and a view transform, which are essential to rendering and visibility.

## 3.2 Perspective Camera

### What it is

A PerspectiveCamera simulates human vision. Objects farther away appear smaller.

### Why it exists

Most 3D scenes are designed to feel natural and immersive, and perspective projection matches what humans expect.

### How it works internally

It uses a field of view, an aspect ratio, a near plane, and a far plane. Vertices are projected according to those values.

### When to use it

Use it for first-person games, third-person games, architectural scenes, and realistic virtual environments.

### Common mistakes

A common mistake is to choose an extreme field of view, which can create distortion.

### Performance considerations

Perspective projection is standard and inexpensive. The main cost comes from the number of pixels rendered rather than the projection math itself.

### Real-world uses

It is the default camera choice for most interactive 3D applications.

### Implementation example

```javascript
const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
);

camera.position.set(0, 2, 8);
camera.lookAt(0, 0, 0);
```

### Beginner explanation

Perspective makes far objects look smaller.

### Intermediate explanation

The field of view controls how much of the scene is visible.

### Advanced explanation

The projection matrix is derived from the frustum and the aspect ratio.

## 3.3 Orthographic Camera

### What it is

An OrthographicCamera renders without perspective distortion. Objects appear the same size no matter how far away they are.

### Why it exists

It is useful when you want exact measurements or a more technical, blueprint-like look.

### How it works internally

It uses a rectangular viewing box instead of a frustum with perspective. The projection is parallel.

### When to use it

Use it for:

- isometric interfaces
- 2.5D games
- CAD or engineering tools
- UI overlays that need precise alignment

### Common mistakes

A beginner might choose orthographic projection for a game that should feel natural, but it can look less immersive.

### Performance considerations

It is efficient and often simpler to reason about for 2D-like interactions.

### Real-world uses

It is common in strategy games, level editors, and technical visualization.

### Implementation example

```javascript
const camera = new THREE.OrthographicCamera(
  -5,
  5,
  5,
  -5,
  0.1,
  100
);
```

### Beginner explanation

Orthographic means no perspective distortion.

### Intermediate explanation

It is useful when size and alignment matter more than realism.

### Advanced explanation

Orthographic projection maps world space to view space using a parallel projection matrix.

## 3.4 Camera movement basics

### What it is

Camera movement means changing the camera’s position and orientation over time.

### Why it exists

Without movement, the user would only see a fixed view.

### How it works internally

The camera’s transform is updated each frame. Then the rendering system uses the updated view matrix to render the scene.

### When to use it

Use it for exploration, gameplay, cinematic movement, or user-controlled navigation.

### Common mistakes

A beginner often updates the camera too abruptly or forgets to smooth the motion.

### Performance considerations

Camera movement is simple, but many expensive operations can quickly accumulate if they are done per object per frame.

### Real-world uses

It is central to all interactive first-person and third-person experiences.

### Implementation pattern

```javascript
camera.position.x += 0.01;
camera.lookAt(0, 0, 0);
```

## 3.5 First-person camera

### What it is

A first-person camera is a camera placed at the eye level of a character or avatar.

### Why it exists

It gives the player the feeling of being inside the world.

### How it works internally

It usually uses mouse input to change yaw and pitch and keyboard input to move forward, back, left, and right.

### When to use it

Use it for shooters, exploration games, and immersive role-playing experiences.

### Common mistakes

A beginner often forgets that the camera must be constrained to realistic motion and that the view should not rotate freely without limits.

### Performance considerations

The cost is low, but the input logic must remain stable and frame-rate independent.

### Real-world uses

It is used in FPS games and simulation tools.

## 3.6 Third-person camera

### What it is

A third-person camera is positioned behind or beside a character.

### Why it exists

It gives the player a view of the character while still allowing the character to be seen.

### How it works internally

The camera often follows the character’s position and uses a target vector to stay behind them.

### When to use it

Use it for adventure games, action games, and character-driven experiences.

### Common mistakes

A common issue is camera clipping through walls or the terrain. Collision avoidance becomes important.

### Performance considerations

Third-person cameras are still inexpensive, but smoothing and collision checks can become noticeable if done poorly.

### Real-world uses

This is the standard camera style for many action games.

## 3.7 OrbitControls

### What it is

OrbitControls is a helper that allows the camera to orbit around a target object.

### Why it exists

It makes camera manipulation in a browser much easier than writing custom mouse logic from scratch.

### How it works internally

It listens to mouse or touch events and updates the camera’s position and target based on the user’s movement.

### When to use it

Use it for product viewers, model inspection, architectural walkthroughs, and scene exploration.

### Common mistakes

A beginner might forget to add the controls to the animation loop or to enable damping for smoother motion.

### Performance considerations

OrbitControls is lightweight and often good for editors and inspection tools.

### Example

```javascript
import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.target.set(0, 0, 0);
```

### Beginner explanation

OrbitControls lets you rotate the camera around the scene with the mouse.

### Intermediate explanation

It simplifies scene inspection and object exploration.

### Advanced explanation

The controls update the camera’s transform from pointer movement and maintain smooth motion using damping.

## 3.8 FlyControls

### What it is

FlyControls provide a first-person-like flying camera experience.

### Why it exists

They are useful for navigation in large 3D scenes and editors.

### How it works internally

They listen for keyboard and mouse input and move the camera along its local axes.

### When to use them

Use them for architectural tools, simulation environments, or quick scene navigation.

### Common mistakes

A beginner might forget to update the camera’s look direction as well as its position.

### Performance considerations

They are lightweight, but poor input handling can make the motion feel unstable.

## 3.9 Camera smoothing

### What it is

Camera smoothing means making the camera move gradually rather than abruptly.

### Why it exists

Smooth motion feels more natural and professional.

### How it works internally

The camera position or target is interpolated toward a desired value over time.

### When to use it

Use it for third-person cameras, follow cameras, and cinematic transitions.

### Common mistakes

A beginner might use a fixed speed that causes overshoot or lag.

### Performance considerations

Smooth motion is inexpensive and often worth it.

### Example

```javascript
const desiredPosition = new THREE.Vector3(0, 2, 6);
const smoothness = 0.08;

camera.position.lerp(desiredPosition, smoothness);
```

## 3.10 PointerLockControls

### What it is

PointerLockControls is a helper that captures mouse movement and allows a camera to look around naturally like in a game.

### Why it exists

It makes first-person navigation simple and immersive.

### How it works internally

The browser’s Pointer Lock API is used to lock the mouse pointer to the canvas and report movement deltas.

### When to use it

Use it for first-person games, immersive walkthroughs, and editors.

### Common mistakes

A beginner often forgets to handle pointer lock events or to unlock the pointer when the user presses Escape.

### Performance considerations

It is efficient, but you should keep the input logic simple and predictable.

## 3.11 Updating the project to include camera motion

We will extend the project so that the camera slowly orbits the scene while the cube continues to animate. This gives the user a more cinematic view of the scene.

```html
<script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/OrbitControls.js';

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  document.body.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0x0f172a);

  const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(0, 2, 6);

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;
  controls.target.set(0, 0, 0);

  const group = new THREE.Group();
  scene.add(group);

  const geometry = new THREE.BoxGeometry(1, 1, 1);
  const material = new THREE.MeshStandardMaterial({ color: 0x38bdf8 });
  const cube = new THREE.Mesh(geometry, material);
  group.add(cube);

  const clock = new THREE.Clock();

  function animate() {
    requestAnimationFrame(animate);

    const elapsed = clock.getElapsedTime();
    group.position.y = Math.sin(elapsed * 1.5) * 0.75;
    group.rotation.y = elapsed;
    group.scale.setScalar(1 + Math.sin(elapsed * 2) * 0.1);

    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- We import OrbitControls from the examples folder.
- We create the controls and attach them to the renderer’s canvas.
- The controls update the camera automatically as the user drags the mouse.
- The animation loop updates the object and then calls controls.update().

## 3.12 Mini project: build a rotating exhibit

Create a small museum-like scene where the camera orbits a central artifact. Add a floor and a few objects around it to make the scene feel more complete.

## 3.13 Challenge problem

Implement a third-person camera that follows a moving character from behind. Make the camera smoothly lag behind the character and keep the character centered in the view.

## 3.14 Troubleshooting

### Problem: The camera does not move

Check whether the controls update function is being called every frame.

### Problem: The scene looks clipped

Review the near and far distances of the camera.

### Problem: The camera moves unexpectedly

Ensure that your target is updated and that your input direction is not being doubled.

## 3.15 Summary

In this chapter, you learned the role of the camera in a Three.js scene. You explored perspective and orthographic projection, first-person navigation, third-person following, orbit controls, fly controls, and camera smoothing. The project now has a more interactive camera that can be driven by the user and by animation.

The next chapter will introduce more shapes, geometry types, and materials so that the scene becomes less box-like and more interesting.
