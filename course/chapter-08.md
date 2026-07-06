# Chapter 8 — Textures, UV Mapping, and Surface Detail

The project is now interactive and can respond to the user. In this chapter, we make the objects visually richer by adding textures. Textures are one of the most important tools in 3D graphics because they allow a mesh to look detailed without adding much geometry.

We will continue evolving the same project in [course/project.html](course/project.html) so that each chapter builds on the previous one instead of replacing it.

## 8.1 What textures are

### What they are

Textures are image data applied to the surface of a 3D object. They can define color, roughness, bump detail, normal detail, and more.

### Why they exist

Geometry alone is not enough to make a surface look rich. Textures allow the same simple mesh to look like stone, wood, metal, skin, or fabric.

### How they work internally

A texture is sampled by the shader for each fragment. The shader uses the object’s UV coordinates to know where on the image to read.

### When to use them

Use textures for color detail, decals, roughness, metallic behavior, and surface imperfections.

### Common mistakes

A beginner often forgets that a texture needs UV coordinates to be meaningful. Without UVs, the texture cannot be mapped properly.

### Performance considerations

Textures can be expensive in memory and bandwidth. Correct sizing matters significantly.

### Real-world uses

Textures are used in all modern games, product visuals, games, and interfaces.

### Step-by-step implementation

We will:

1. create a TextureLoader
2. load an image from a URL
3. assign the texture to a material
4. adjust the material to make the surface respond properly

### Beginner explanation

A texture is like wrapping a picture around a 3D object.

### Intermediate explanation

The shader uses UV coordinates to decide where each part of the texture appears.

### Advanced explanation

Textures are sampled in GPU memory and passed to shaders, where they influence the final pixel color and material response.

## 8.2 UV mapping

### What it is

UV mapping is the process of assigning two-dimensional coordinates to the vertices of a 3D surface.

### Why it exists

The GPU needs a way to map a 2D image onto a 3D surface. UVs provide that mapping.

### How they work internally

Each vertex gets a UV coordinate that tells the shader which part of the texture should be associated with that point.

### When to use them

Use UVs whenever you apply textures to geometry.

### Common mistakes

A beginner might use the same image across a mesh without understanding that the UV layout determines how the texture appears.

### Performance considerations

Good UVs are important for quality and efficiency. Bad UVs can cause visible stretching or wasted texture space.

### Real-world uses

UV mapping is used in character clothing, environment materials, decals, and product textures.

### Visual idea

```text
3D surface -> UV coordinates -> 2D image -> texture on the surface
```

## 8.3 Texture filtering

### What it is

Texture filtering determines how a texture is sampled when it is scaled or viewed from different distances.

### Why it exists

Textures need to look good when they are viewed at different resolutions and angles.

### How it works internally

The GPU uses filtering modes such as nearest, linear, mipmapping, and anisotropic filtering.

### When to use them

Use them to improve appearance and reduce aliasing.

### Common mistakes

A beginner might load a texture at a very small size and then scale it up, causing blurry results.

### Performance considerations

Higher-quality filtering can cost more memory and bandwidth.

### Example

```javascript
texture.minFilter = THREE.LinearMipmapLinearFilter;
texture.magFilter = THREE.LinearFilter;
```

## 8.4 Normal maps

### What they are

Normal maps are textures that fake detailed surface direction without changing the actual geometry.

### Why they exist

They make surfaces appear bumpy or detailed while keeping the mesh lightweight.

### How they work internally

They store a perturbed normal vector, which the shader uses to change the lighting response.

### When to use them

Use them for brick, stone, fabric, or rough surfaces.

### Common mistakes

A beginner might expect a normal map to add visible geometry. It does not; it changes lighting only.

### Performance considerations

They are highly efficient and a standard technique in modern graphics.

## 8.5 Roughness and metalness maps

### What they are

These maps control how rough or metallic a surface appears.

### Why they exist

They let the material respond to light in a physically plausible way.

### How they work internally

The shader reads the texture values and adjusts the material response accordingly.

### When to use them

Use them for realistic materials such as wood, metal, plastic, and stone.

### Common mistakes

A beginner might use a roughness map without understanding that it changes how reflections behave.

### Performance considerations

They are lightweight relative to more complex effects.

## 8.6 Environment maps

### What they are

Environment maps are textures that represent the surroundings of an object, usually for reflections.

### Why they exist

They let surfaces reflect the environment, which makes materials look more realistic.

### How they work internally

The shader samples the environment texture based on the viewing direction and the surface normal.

### When to use them

Use them for reflective materials like metal, glass, or polished surfaces.

### Common mistakes

A beginner might expect an environment map to work without a reflective material.

### Performance considerations

They can be expensive, especially in high resolution.

## 8.7 Updating the project with textures

We will add a textured ground plane and a textured cube. The scene will become more varied and realistic.

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

  const textureLoader = new THREE.TextureLoader();
  const colorTexture = textureLoader.load('https://threejs.org/examples/textures/uv_grid_opengl.jpg');

  colorTexture.wrapS = THREE.RepeatWrapping;
  colorTexture.wrapT = THREE.RepeatWrapping;
  colorTexture.repeat.set(4, 4);

  const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(12, 12),
    new THREE.MeshStandardMaterial({ map: colorTexture, side: THREE.DoubleSide })
  );
  plane.rotation.x = -Math.PI / 2;
  scene.add(plane);

  const box = new THREE.Mesh(
    new THREE.BoxGeometry(1.6, 1.6, 1.6),
    new THREE.MeshStandardMaterial({ map: colorTexture })
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

- A TextureLoader loads an image.
- The texture is repeated across the plane to create visible tiling.
- The material uses the texture as its map.
- The box uses the same texture, demonstrating that the same texture can be shared.

## 8.8 Mini project: create a textured room

Add walls, a floor, and a crate using different textures and observe how the same UV layout changes the appearance on each object.

## 8.9 Challenge problem

Create a simple material palette where each shape uses a different texture or texture repetition to show the influence of mapping.

## 8.10 Troubleshooting

### Problem: The texture does not appear

Check the URL, the image format, and whether the material is using the texture map.

### Problem: The texture looks stretched

Inspect the UV layout or adjust the geometry’s dimensions.

### Problem: The texture looks blurry

Use a higher-resolution image or adjust the filtering settings.

## 8.11 Summary

In this chapter, you learned what textures are, why UV mapping is essential, and how texture filtering and mapping influence appearance. You also saw how to apply a texture to a material and how texture mapping can make a scene feel much more detailed.

The next chapter will introduce environment maps, skyboxes, cube textures, and HDR-based lighting, which make materials even more realistic.
