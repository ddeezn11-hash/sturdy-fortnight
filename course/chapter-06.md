# Chapter 6 — Animation Timing, Frame Independence, and Input

The project now looks more complete visually, but it still feels like a static demonstration. In this chapter, we will make it interactive. The scene will respond to time, keyboard input, and mouse input. This is where a 3D scene becomes an experience rather than a still image.

We will continue to evolve the same project file in [course/project.html](course/project.html), adding logic that can be reused in games, editors, and simulations.

## 6.1 What animation timing is

### What it is

Animation timing is the process of making motion depend on time rather than on an arbitrary frame count.

### Why it exists

A simulation should not slow down or speed up just because the device is fast or slow. Time-based animation leads to predictable behavior.

### How it works internally

The engine uses elapsed time between frames to decide how far an object should move or rotate.

### When to use it

Use it for movement, transitions, gameplay, physics, and any continuously changing behaviors.

### Common mistakes

A beginner might rotate an object by a fixed amount per frame and unintentionally make the motion device-dependent.

### Performance considerations

Time-based updates are cheap, but they should remain simple and avoid heavy per-frame work.

### Real-world uses

They are standard in games, UI motion systems, cameras, and simulations.

## 6.2 Clock

### What it is

Clock is a Three.js utility that measures elapsed time.

### Why it exists

It gives you a simple way to access timing information from the animation loop.

### How it works internally

It uses the browser’s high-resolution clock to track time between frames.

### When to use it

Use it whenever you need the current elapsed time or delta time.

### Common mistakes

A beginner might create multiple clocks unnecessarily or forget to use the correct method for time retrieval.

### Example

```javascript
const clock = new THREE.Clock();

function animate() {
  requestAnimationFrame(animate);
  const elapsed = clock.getElapsedTime();
  cube.rotation.y = elapsed;
  renderer.render(scene, camera);
}
```

## 6.3 Delta time

### What it is

Delta time is the amount of time that passed since the previous frame.

### Why it exists

It makes movement frame-rate independent.

### How it works internally

The engine measures the time between frames and uses that value as a multiplier for movement.

### When to use it

Use delta time when moving objects or characters based on speed.

### Common mistakes

A beginner might forget to multiply by delta time, causing movement to speed up on faster machines.

### Performance considerations

Delta time is efficient and essential for smooth motion.

### Formula

$$
\text{newPosition} = \text{oldPosition} + \text{velocity} \cdot \Delta t
$$

This formula means movement depends on time, not on the number of frames.

## 6.4 requestAnimationFrame

### What it is

requestAnimationFrame is the browser API that tells the browser to run a function before the next repaint.

### Why it exists

It is the proper way to create smooth animation in the browser.

### How it works internally

The browser schedules the callback according to the display refresh rate.

### When to use it

Use it for animation loops, visual updates, and interactive scenes.

### Common mistakes

A common mistake is to use setInterval for animation. requestAnimationFrame is better because it aligns with rendering.

### Performance considerations

It is efficient and browser-friendly.

### Example

```javascript
function animate() {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
}

animate();
```

## 6.5 FPS-independent movement

### What it is

FPS-independent movement means that the speed of movement stays consistent even when the frame rate changes.

### Why it exists

If movement is tied to frames, the object will move faster on a high-refresh-rate device and slower on a low-refresh-rate one.

### How it works internally

The movement amount is multiplied by delta time.

### When to use it

Use it in games, vehicles, character controllers, camera movement, and physics.

### Common mistakes

A beginner might update position by a constant amount each frame, which is not frame-rate independent.

### Example

```javascript
const speed = 2;
const delta = clock.getDelta();

cube.position.x += speed * delta;
```

## 6.6 Keyboard controls

### What it is

Keyboard controls let the user move or manipulate the scene using the keyboard.

### Why they exist

They are essential for interactions in games and editors.

### How they work internally

The browser reports keydown and keyup events. Your code tracks which keys are currently pressed and updates the camera or object accordingly.

### When to use them

Use them for movement, actions, shortcuts, and menu navigation.

### Common mistakes

A beginner might forget to handle key release events and end up with movement that sticks.

### Example

```javascript
const keys = {};

window.addEventListener('keydown', (event) => {
  keys[event.code] = true;
});

window.addEventListener('keyup', (event) => {
  keys[event.code] = false;
});
```

## 6.7 Mouse controls

### What they are

Mouse controls allow the user to view or interact with the scene using the pointer.

### Why they exist

Mouse input is natural for camera rotation, object selection, and UI interaction.

### How they work internally

The browser fires mousemove, mousedown, mouseup, and click events. Your code uses those to update transforms or camera orientation.

### When to use them

Use them for orbiting the camera, aiming, clicking objects, or dragging items.

### Common mistakes

A beginner might not normalize movement values and end up with jerky or oversensitive controls.

### Performance considerations

Input handling is cheap, but excessive event processing can become noticeable if you do too much work per movement event.

## 6.8 Pointer Lock API

### What it is

The Pointer Lock API lets you capture mouse movement so that the pointer is locked to the browser window or canvas.

### Why it exists

It is ideal for first-person games and camera-based interactions.

### How it works internally

The browser enters a pointer-locked state and reports movement deltas while the pointer is hidden.

### When to use it

Use it for immersive mouse look.

### Common mistakes

A beginner might forget to unlock the pointer when pressing Escape or leaving the experience.

### Example

```javascript
renderer.domElement.addEventListener('click', () => {
  renderer.domElement.requestPointerLock();
});

document.addEventListener('pointerlockchange', () => {
  if (document.pointerLockElement !== renderer.domElement) {
    console.log('Pointer lock released');
  }
});
```

## 6.9 PointerLockControls

### What it is

PointerLockControls is a Three.js helper built around the Pointer Lock API.

### Why it exists

It simplifies first-person camera control.

### How it works internally

It stores yaw and pitch values and converts them into camera rotation.

### When to use it

Use it for FPS-style setups.

### Common mistakes

A beginner may not clamp the pitch, causing the camera to flip upside down.

### Example

```javascript
import { PointerLockControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/PointerLockControls.js';

const controls = new PointerLockControls(camera, document.body);
scene.add(controls.getObject());
```

## 6.10 Updating the project with keyboard movement

We will add keyboard movement to the cube or camera so the scene can be controlled interactively.

```html
<script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/OrbitControls.js';

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.shadowMap.enabled = true;
  document.body.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0x111827);

  const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(3, 2, 5);

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;

  const cube = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshStandardMaterial({ color: 0x38bdf8 })
  );
  cube.position.set(0, 1, 0);
  scene.add(cube);

  const keys = {};
  window.addEventListener('keydown', (event) => { keys[event.code] = true; });
  window.addEventListener('keyup', (event) => { keys[event.code] = false; });

  const clock = new THREE.Clock();

  function animate() {
    requestAnimationFrame(animate);
    const delta = clock.getDelta();

    if (keys['ArrowLeft']) cube.position.x -= 2 * delta;
    if (keys['ArrowRight']) cube.position.x += 2 * delta;
    if (keys['ArrowUp']) cube.position.z -= 2 * delta;
    if (keys['ArrowDown']) cube.position.z += 2 * delta;

    cube.rotation.y += 2 * delta;
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- The code listens for keyboard events and stores pressed keys.
- The animation loop checks the key states each frame.
- The cube position changes based on delta time.
- The object rotates at a speed scaled by time.

## 6.11 Mini project: build a simple character controller

Create a small scene where the cube moves forward, backward, left, and right using the arrow keys. Add a ground plane and a few obstacles.

## 6.12 Challenge problem

Create a pointer-locked first-person camera that moves using WASD and rotates with the mouse. Add a simple collision check so the camera cannot move through walls.

## 6.13 Troubleshooting

### Problem: Movement is too fast or too slow

Check your use of delta time and the velocity value.

### Problem: Keyboard input feels sticky

Ensure that keydown and keyup are both handled correctly.

### Problem: The camera does not rotate smoothly

Make sure the animation loop remains active and the renderer is being updated.

## 6.14 Summary

In this chapter, you learned how to make your scene feel alive through time-based animation, delta time, requestAnimationFrame, keyboard controls, mouse controls, and pointer lock. These concepts are essential for turning a simple visual scene into an interactive experience.

The next chapter will focus on object interaction, including raycasting, selection, shooting rays, and collision detection basics.
