# Chapter 10 — Fog, Particles, Sprites, and Billboards

The scene now has a richer surface and a surrounding environment. In this chapter, we add atmospheric and particle effects. These are common techniques for making scenes feel alive, dense, and visually interesting without needing massive geometry.

We continue extending [course/project.html](course/project.html) so the project grows consistently from one chapter to the next.

## 10.1 What fog is

### What it is

Fog is a rendering effect that causes distant objects to fade into a color.

### Why it exists

It helps create depth, reduce visible popping, and make large scenes feel more natural.

### How it works internally

The renderer computes how much of the object’s color should be blended with the fog color based on its distance from the camera.

### When to use it

Use fog for outdoor scenes, large environments, and atmospheric effects.

### Common mistakes

A beginner might use fog without adjusting the camera far plane, causing visible artifacts or unrealistic transitions.

### Performance considerations

Fog is inexpensive and often a great improvement for large scenes.

### Example

```javascript
scene.fog = new THREE.Fog(0x111827, 1, 30);
```

## 10.2 Particle systems

### What they are

Particle systems are groups of many small points, sprites, or quads that together form effects such as smoke, dust, fire, sparks, rain, or stars.

### Why they exist

They are efficient ways to create complex-looking effects without modeling every detail as geometry.

### How they work internally

A particle system manages many small elements, each with its own position, size, rotation, and color. The renderer draws them as a batch or as many tiny quads.

### When to use them

Use them for weather, magic effects, explosions, and environmental detail.

### Common mistakes

A beginner may create too many particles and hurt performance.

### Performance considerations

Particle systems can be expensive when the count is high. Use instancing or lower counts when possible.

## 10.3 Sprite rendering

### What it is

Sprites are flat images that always face the camera.

### Why they exist

They are perfect for icons, particles, decals, and simple visual effects.

### How they work internally

A sprite uses a texture and a quad that is oriented toward the camera.

### When to use them

Use them for trees, billboards, indicators, particles, and simple visual objects.

### Common mistakes

A beginner might forget that a sprite is always oriented to the camera and therefore does not behave like a full 3D object.

### Performance considerations

Sprites are lightweight and are a common optimization technique.

## 10.4 Billboards

### What they are

Billboards are objects that always face the camera so they appear to have depth while being essentially flat.

### Why they exist

They are efficient when a detailed 3D model is unnecessary.

### How they work internally

A billboard is often implemented as a sprite or a plane that is constantly aligned with the camera.

### When to use them

Use them for trees, distant objects, UI markers, and low-cost decorations.

### Common mistakes

A beginner might rotate the billboard incorrectly and create a visible “popping” or weird orientation.

### Performance considerations

They are a great way to add visual detail at low cost.

## 10.5 Updating the project with fog and particles

We will add fog to the scene and create a simple particle cloud to make the environment feel dynamic.

```html
<script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/OrbitControls.js';

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  document.body.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0x111827);
  scene.fog = new THREE.Fog(0x111827, 1, 30);

  const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(4, 3, 6);

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;

  const particleCount = 200;
  const positions = new Float32Array(particleCount * 3);
  const colors = new Float32Array(particleCount * 3);

  for (let i = 0; i < particleCount; i++) {
    positions[i * 3 + 0] = (Math.random() - 0.5) * 20;
    positions[i * 3 + 1] = Math.random() * 8;
    positions[i * 3 + 2] = (Math.random() - 0.5) * 20;

    colors[i * 3 + 0] = 1;
    colors[i * 3 + 1] = 1;
    colors[i * 3 + 2] = 1;
  }

  const particleGeometry = new THREE.BufferGeometry();
  particleGeometry.setAttribute('position', new THREE.BufferAttribute(positions, 3));
  particleGeometry.setAttribute('color', new THREE.BufferAttribute(colors, 3));

  const particleMaterial = new THREE.PointsMaterial({ size: 0.08, vertexColors: true, transparent: true, opacity: 0.7 });
  const particles = new THREE.Points(particleGeometry, particleMaterial);
  scene.add(particles);

  function animate() {
    requestAnimationFrame(animate);
    particles.rotation.y += 0.001;
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- A fog object is added to the scene to fade distant objects.
- Points are created in a buffer geometry so many small particles can be drawn efficiently.
- A PointsMaterial makes the particles visible as tiny points.
- The particles slowly rotate to create motion.

## 10.6 Mini project: create a storm cloud effect

Use a particle system to create rain, smoke, or floating dust. Adjust the colors and sizes to make the effect feel atmospheric.

## 10.7 Challenge problem

Make a billboard-based tree or glowing icon that always faces the camera while remaining in the scene.

## 10.8 Troubleshooting

### Problem: The particles do not appear

Check whether the points material is using a visible size and whether the buffer data is valid.

### Problem: The fog looks wrong

Adjust the near and far distances so the effect feels natural.

## 10.9 Summary

In this chapter, you learned how fog, particles, sprites, and billboards can add atmosphere and detail to a scene without requiring heavy geometry. These techniques are widely used in games and interactive experiences because they are powerful and relatively efficient.

The next chapter will focus on asset loading, managers, and importing external models into the project.
