# Chapter 11 — Loading Assets, Managers, and External Models

The project now has a visible world, lighting, interaction, and atmosphere. In this chapter, we move from built-in shapes to external content. Real-world 3D applications rarely consist only of procedurally generated geometry. Instead, they often import models, textures, animations, and sounds from files.

We continue building the same project in [course/project.html](course/project.html), but the course now shifts toward assets and asset pipelines.

## 11.1 What asset loading is

### What it is

Asset loading is the process of fetching files such as images, models, audio, or data and turning them into usable Three.js objects.

### Why it exists

Most 3D content comes from external tools such as Blender, Maya, Substance, or even hand-authored JSON. The browser must load those resources before they can be rendered.

### How it works internally

Three.js uses loaders such as TextureLoader, GLTFLoader, or AudioLoader to fetch and parse the files. The data is then reconstructed into geometry, materials, animations, or sounds.

### When to use it

Use asset loading whenever your scene depends on external files or media.

### Common mistakes

A beginner often forgets that assets load asynchronously, so code that uses the loaded object must wait until loading finishes.

### Performance considerations

Asset loading can affect startup time. Use compressed assets, appropriate textures, and sensible loading strategies.

### Real-world uses

Asset loading is used in games, product configurators, digital twins, and architectural visualization.

## 11.2 LoadingManager

### What it is

LoadingManager is a helper that tracks the state of multiple asynchronous loads.

### Why it exists

It helps manage loading progress, errors, and completion events for many assets.

### How it works internally

It dispatches callbacks for onStart, onLoad, onProgress, and onError.

### When to use it

Use it when a scene needs multiple assets and you want to coordinate their loading.

### Common mistakes

A beginner might not handle progress or errors, causing the app to appear broken without explanation.

### Performance considerations

A manager makes the pipeline cleaner, but it should not add significant overhead.

### Example

```javascript
const loadingManager = new THREE.LoadingManager();

loadingManager.onStart = () => console.log('Loading started');
loadingManager.onProgress = (url, loaded, total) => console.log(url, loaded, total);
loadingManager.onLoad = () => console.log('Loading complete');
loadingManager.onError = (url) => console.error('Failed to load', url);
```

## 11.3 TextureLoader

### What it is

TextureLoader loads image files and creates Texture objects.

### Why it exists

It provides a simple way to bring images into the scene.

### How it works internally

The loader fetches the image and creates a texture object that can be used by materials.

### When to use it

Use it whenever you want to apply images to surfaces.

### Common mistakes

A beginner might forget to call texture.needsUpdate or to set wrapping/filtering appropriately.

### Performance considerations

Use appropriately sized textures and compress them where possible.

## 11.4 GLTFLoader

### What it is

GLTFLoader loads GLTF and GLB files, which are standard 3D asset formats.

### Why it exists

GLTF is a compact and widely supported format for 3D scenes and models.

### How it works internally

It parses the file, reconstructs scene graph nodes, meshes, materials, textures, and animations, and then returns a group or scene object.

### When to use it

Use it for importing complex assets from Blender, export pipelines, or online repositories.

### Common mistakes

A beginner might forget that a loaded model may contain many children and materials, so the model should be inspected before it is manipulated.

### Performance considerations

Models can be large. Use optimized exports and simplify geometry where necessary.

### Example

```javascript
import { GLTFLoader } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/loaders/GLTFLoader.js';

const loader = new THREE.GLTFLoader();
loader.load('model.glb', (gltf) => {
  scene.add(gltf.scene);
});
```

## 11.5 Loading Blender models

### What it is

Blender models are commonly exported as GLTF or GLB files for use in the web.

### Why it exists

Blender is a common authoring tool for 3D artists, and GLTF is a practical web-friendly exchange format.

### How it works internally

The export process saves geometry, materials, transformations, lights, and animations. The loader rehydrates those assets in the browser.

### When to use it

Use it when you want to bring professional content into the scene.

### Common mistakes

A beginner may export the model without applying transforms, causing scaling or rotation issues.

### Performance considerations

Large Blender exports can be heavy. Optimize the mesh and texture data before shipping.

## 11.6 Updating the project with a loaded model

We will add a simple placeholder model loading step. If the asset cannot be loaded in the environment, the code will still render the rest of the scene correctly.

```html
<script type="module">
  import * as THREE from 'three';
  import { OrbitControls } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/controls/OrbitControls.js';
  import { GLTFLoader } from 'https://cdn.jsdelivr.net/npm/three@0.160.0/examples/jsm/loaders/GLTFLoader.js';

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

  const loader = new GLTFLoader();
  loader.load(
    'https://threejs.org/examples/models/gltf/duck/duck.gltf',
    (gltf) => {
      gltf.scene.scale.set(2, 2, 2);
      gltf.scene.position.set(0, 0.5, 0);
      scene.add(gltf.scene);
    },
    undefined,
    (error) => {
      console.error('Model loading failed:', error);
    }
  );

  const ambient = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambient);

  const directional = new THREE.DirectionalLight(0xffffff, 1.2);
  directional.position.set(3, 5, 4);
  scene.add(directional);

  function animate() {
    requestAnimationFrame(animate);
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- The GLTFLoader is imported.
- The loader fetches a model from a URL.
- Once loaded, the model is scaled and placed into the scene.
- If loading fails, an error message is shown in the console.

## 11.7 Mini project: import an object and place it in a room

Load a model and place it in a simple scene with a floor, a lighting setup, and an orbiting camera.

## 11.8 Challenge problem

Create a loading manager that reports progress while multiple assets are being loaded and displays a small loading overlay in the page.

## 11.9 Troubleshooting

### Problem: The model does not appear

Check the URL and ensure the model is loaded before use. Also verify that the scene has a camera and lighting.

### Problem: The model is huge or tiny

Scale the loaded object after it is added to the scene.

### Problem: The model is rotated strangely

Inspect the exported model’s orientation or adjust its rotation after loading.

## 11.10 Summary

In this chapter, you learned about asset loading, LoadingManager, TextureLoader, GLTFLoader, and the practice of bringing external models into a Three.js scene. The project now demonstrates how content from external files can be incorporated into the growing application.

The next chapter will introduce animations and animation mixing for imported models.
