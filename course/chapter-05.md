# Chapter 5 — Lights, Shadows, and Visual Realism

The scene now has many objects, but it still lacks the visual depth that makes a 3D world feel real. In this chapter, we add lights and shadows. Lighting is one of the most important parts of 3D graphics because it determines how surfaces appear and how the viewer perceives shape, depth, and mood.

We will continue to use the same growing project file so the lessons build on one another instead of replacing what came before.

## 5.1 Why lighting matters

### What it is

Lighting is the use of light sources to illuminate a scene. It affects color, contrast, highlights, and shadows.

### Why it exists

Without lighting, objects would appear flat and visually less meaningful. Lighting provides depth and realism.

### How it works internally

The renderer calculates how light interacts with surfaces based on their normals, material properties, and the position and intensity of light sources.

### When to use it

Use lighting whenever you want the scene to feel physically believable or visually expressive.

### Common mistakes

A beginner often uses only a single color and expects the scene to look realistic. Realistic scenes usually require a combination of lights and thoughtful material choices.

### Performance considerations

More lights and shadows can be expensive. Use them carefully, especially on mobile or lower-powered devices.

### Real-world uses

Lighting is essential for games, architectural visualization, industrial design, film-like scenes, and immersive web experiences.

### Step-by-step implementation

We will add several light sources to the scene and then enable shadows.

### Beginner explanation

Lights make the objects visible and give them shape.

### Intermediate explanation

Lights affect the appearance of a surface based on direction and intensity.

### Advanced explanation

The material and the light sources together determine the reflected radiance seen by the camera.

## 5.2 Ambient Light

### What it is

AmbientLight illuminates everything equally from all directions.

### Why it exists

It is useful for filling in shadows and preventing scenes from becoming too dark.

### How it works internally

It adds a uniform color contribution to every surface.

### When to use it

Use it as a general fill light, especially as a starting point.

### Common mistakes

If you use ambient light alone, the scene can look flat and washed out.

### Performance considerations

Ambient light is inexpensive.

### Example

```javascript
const ambient = new THREE.AmbientLight(0xffffff, 0.35);
scene.add(ambient);
```

## 5.3 Directional Light

### What it is

DirectionalLight simulates sunlight. It comes from a direction rather than a point in space.

### Why it exists

It is perfect for outdoor scenes because all rays are parallel.

### How it works internally

The light has a direction and an intensity. Every surface is lit according to the angle between its normal and the light direction.

### When to use it

Use it for daylight scenes, large outdoor environments, and simple directional illumination.

### Common mistakes

A beginner may place the directional light too close to the scene and get harsh, odd results.

### Performance considerations

Directional lights are efficient and common.

### Example

```javascript
const sun = new THREE.DirectionalLight(0xffffff, 1.2);
sun.position.set(3, 5, 4);
scene.add(sun);
```

## 5.4 Point Light

### What it is

PointLight emits light in all directions from a single point.

### Why it exists

It simulates bulbs, candles, torches, and other local light sources.

### How it works internally

The intensity falls off with distance.

### When to use it

Use it for lamps, explosions, glow effects, or localized illumination.

### Common mistakes

A beginner may make the light too strong, causing the scene to look washed out or noisy.

### Performance considerations

Point lights are more expensive than ambient or directional lights, especially when many are used.

### Example

```javascript
const lamp = new THREE.PointLight(0xffaa66, 15, 10);
lamp.position.set(0, 2, 0);
scene.add(lamp);
```

## 5.5 Spot Light

### What it is

SpotLight emits a cone of light.

### Why it exists

It is used for flashlights, stage lights, car headlights, and dramatic effects.

### How it works internally

It has a position, a direction, a range, and an angle.

### When to use it

Use it for focused illumination.

### Common mistakes

A common mistake is to forget to set the light’s target, which can make the light appear to point in a strange direction.

### Performance considerations

Spot lights are more expensive than point lights and should be used sparingly.

### Example

```javascript
const spotlight = new THREE.SpotLight(0xffffff, 10, 20, Math.PI / 6, 0.3, 1);
spotlight.position.set(0, 4, 3);
spotlight.target.position.set(0, 0, 0);
scene.add(spotlight);
scene.add(spotlight.target);
```

## 5.6 Hemisphere Light

### What it is

HemisphereLight simulates a sky and ground color blend.

### Why it exists

It is useful for outdoor scenes because it creates natural ambient light from above and below.

### How it works internally

It uses two colors: one for the sky and one for the ground.

### When to use it

Use it for outdoor environments and large open spaces.

### Common mistakes

A beginner might use it when a directional light would be more appropriate.

### Performance considerations

It is efficient and often a good baseline light for outdoor scenes.

### Example

```javascript
const hemisphere = new THREE.HemisphereLight(0x87ceeb, 0x3b2f2f, 0.7);
scene.add(hemisphere);
```

## 5.7 Shadows

### What they are

Shadows are dark regions created where light is blocked by an object.

### Why they exist

They add depth, realism, and spatial understanding.

### How they work internally

The engine renders the scene from the light’s point of view to create a shadow map.

### When to use them

Use them to increase visual fidelity, but avoid overusing them if performance is a concern.

### Common mistakes

A common mistake is enabling shadows for every light and object, which can make the scene much slower.

### Performance considerations

Shadows are expensive. Limit them to important light sources and objects.

### Real-world uses

They are central to games, cinematic scenes, and simulations.

## 5.8 Shadow maps

### What they are

Shadow maps are images generated from the perspective of a light source. They store depth information used to determine whether a surface is in shadow.

### Why they exist

The renderer cannot infer occlusion from the scene without some form of depth comparison, so shadow maps provide a practical mechanism.

### How they work internally

The light renders the scene into a depth texture. Later, the main render compares each fragment’s depth to the stored depth for that shadow ray.

### When to use them

Use them for real-time shadowing in most 3D scenes.

### Common mistakes

Beginners often forget to set renderer.shadowMap.enabled = true and to mark objects as casting or receiving shadows.

### Performance considerations

Shadow map resolution and quality affect performance. Larger maps produce better shadows but cost more.

### Example

```javascript
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
```

## 5.9 Enabling shadows in the project

To make the scene more realistic, we will:

- enable shadow maps on the renderer
- make the ground receive shadows
- make the objects cast shadows
- set the light’s shadow properties

```html
<script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/OrbitControls.js';

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  renderer.shadowMap.enabled = true;
  renderer.shadowMap.type = THREE.PCFSoftShadowMap;
  document.body.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0x111827);

  const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(4, 3, 6);

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;

  const ambient = new THREE.AmbientLight(0xffffff, 0.25);
  scene.add(ambient);

  const sun = new THREE.DirectionalLight(0xffffff, 1.1);
  sun.position.set(3, 6, 4);
  sun.castShadow = true;
  sun.shadow.mapSize.set(2048, 2048);
  scene.add(sun);

  const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(12, 12),
    new THREE.MeshStandardMaterial({ color: 0x1f2937, side: THREE.DoubleSide })
  );
  plane.rotation.x = -Math.PI / 2;
  plane.receiveShadow = true;
  scene.add(plane);

  const cube = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshStandardMaterial({ color: 0x38bdf8 })
  );
  cube.position.set(0, 1, 0);
  cube.castShadow = true;
  cube.receiveShadow = true;
  scene.add(cube);

  const clock = new THREE.Clock();

  function animate() {
    requestAnimationFrame(animate);
    const elapsed = clock.getElapsedTime();

    cube.rotation.y = elapsed;
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- The renderer is told to generate shadow maps.
- The directional light is set to cast shadows.
- The plane is told to receive shadows.
- The cube is told to cast and receive shadows.
- The light’s shadow map size is increased for better shadow quality.

## 5.10 Mini project: create a dramatic interior

Build a room-like scene with a single spotlight and a few objects. Use a floor, walls, and a desk lamp effect to practice shadows.

## 5.11 Challenge problem

Set up a scene with both a directional light and a point light. Make the point light act like a lamp, and ensure the scene still remains readable and not overexposed.

## 5.12 Troubleshooting

### Problem: No shadows appear

Make sure shadows are enabled on the renderer and that the light and objects are marked to cast or receive shadows.

### Problem: Shadows look jagged

Increase the shadow map size or reduce the shadow camera range.

### Problem: The scene looks too dark

Add ambient light or lower the intensity of the main light.

## 5.13 Summary

In this chapter, you learned about ambient light, directional light, point light, spot light, and hemisphere light. You also learned how shadow maps work and how to enable realistic shadows in the project. Lighting and shadows are essential for making a 3D scene feel believable rather than flat.

The next chapter will focus on animation timing, frame-rate independence, and input handling so that the scene becomes interactive rather than just visual.
