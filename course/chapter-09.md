# Chapter 9 — Environment Maps, Skyboxes, Cube Textures, and HDR Lighting

The project now has shape, motion, lighting, interaction, and texture. In this chapter, we make the scene feel more immersive by introducing environment maps and skyboxes. These techniques are a major reason 3D scenes feel believable and polished.

We continue editing the same project file so the course remains one continuous development path.

## 9.1 What environment maps are

### What they are

Environment maps are textures that describe the surrounding world. They are often used to represent reflections or the scene background.

### Why they exist

They allow materials to reflect the environment rather than appearing flat or artificial.

### How they work internally

A shader samples the environment map based on the viewing direction and surface normal.

### When to use them

Use them for reflective materials such as metal, polished surfaces, glass, and glossy objects.

### Common mistakes

A beginner may expect an environment map to automatically make everything reflective. It does not; the material must be configured to use it.

### Performance considerations

Environment maps are more expensive than simple color maps, especially at high resolution.

### Real-world uses

They are used in automotive visualization, product showcases, games, and immersive scenes.

## 9.2 Skyboxes

### What they are

A skybox is a large cube or sphere that surrounds the scene and provides a background and environmental context.

### Why they exist

They create the illusion of an enormous environment and make a scene feel larger and more believable.

### How they work internally

The skybox is rendered in the background, usually using cube textures so the surrounding environment can be viewed from any direction.

### When to use them

Use them for outdoor scenes, game worlds, and polished visual experiences.

### Common mistakes

A beginner might use a 2D image as a skybox and get visible stretching or distortion.

### Performance considerations

Cube maps are efficient and standard, but large resolutions can still increase memory use.

## 9.3 Cube textures

### What they are

Cube textures are six images that represent the faces of a cube: right, left, top, bottom, front, and back.

### Why they exist

They are the standard way to represent environment lighting and skyboxes.

### How they work internally

The shader uses the direction of the reflected or refracted ray to sample one of the six faces.

### When to use them

Use them anytime you need a 360-degree environment representation.

### Common mistakes

A beginner might forget that the six textures must line up correctly.

### Performance considerations

Cube textures are efficient but can be expensive at very high resolution.

## 9.4 HDR environments

### What they are

HDR environments are high dynamic range images that capture a broad range of brightness values.

### Why they exist

They produce more realistic lighting and reflections than standard low dynamic range images.

### How they work internally

HDR textures store more information per pixel, including highlights and bright light sources.

### When to use them

Use them for physically based rendering and realistic lighting setups.

### Common mistakes

A beginner may not know that HDR requires proper tone mapping and exposure control to be displayed well on a standard screen.

### Performance considerations

HDR textures use more memory but are worth it for high-quality scenes.

## 9.5 Updating the project with a skybox and reflections

We will add an environment texture and use it for the background and a reflective material. This will make the scene feel more three-dimensional and polished.

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

  const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(4, 3, 6);

  const controls = new OrbitControls(camera, renderer.domElement);
  controls.enableDamping = true;

  const cubeTextureLoader = new THREE.CubeTextureLoader();
  const environmentTexture = cubeTextureLoader.load([
    'https://threejs.org/examples/textures/cube/Bridge2/posx.jpg',
    'https://threejs.org/examples/textures/cube/Bridge2/negx.jpg',
    'https://threejs.org/examples/textures/cube/Bridge2/posy.jpg',
    'https://threejs.org/examples/textures/cube/Bridge2/negy.jpg',
    'https://threejs.org/examples/textures/cube/Bridge2/posz.jpg',
    'https://threejs.org/examples/textures/cube/Bridge2/negz.jpg'
  ]);

  scene.background = environmentTexture;
  scene.environment = environmentTexture;

  const box = new THREE.Mesh(
    new THREE.BoxGeometry(1.6, 1.6, 1.6),
    new THREE.MeshPhysicalMaterial({ metalness: 1, roughness: 0.1, envMap: environmentTexture })
  );
  box.position.set(0, 1, 0);
  scene.add(box);

  const ambient = new THREE.AmbientLight(0xffffff, 0.35);
  scene.add(ambient);

  const directional = new THREE.DirectionalLight(0xffffff, 1.2);
  directional.position.set(3, 5, 4);
  scene.add(directional);

  function animate() {
    requestAnimationFrame(animate);
    box.rotation.y += 0.01;
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- A CubeTextureLoader loads six images that form an environment.
- The scene background is assigned the texture, making the skybox visible.
- The environment is also assigned to the scene so reflective materials can use it.
- A MeshPhysicalMaterial is used because it supports environment reflections well.

## 9.6 Mini project: create a reflective showroom

Set up a reflective object inside a room-like environment and use a skybox so the object appears to sit inside a larger world.

## 9.7 Challenge problem

Try updating the scene so that the box and a ground plane both pick up reflections from the environment map in a way that feels plausible.

## 9.8 Troubleshooting

### Problem: The environment map does not appear

Double-check that the cube texture URLs are correct and that the scene background and environment have been assigned.

### Problem: The reflections look wrong

Ensure the material uses a physically based material and that the scene environment is assigned.

## 9.9 Summary

In this chapter, you learned about environment maps, skyboxes, cube textures, and HDR-style lighting. These techniques make materials look significantly more realistic and help the scene feel larger and more immersive.

The next chapter will focus on fog, particles, sprites, billboards, and atmospheric effects.
