# Chapter 4 — Geometry, Meshes, and Materials

The project is no longer just a cube. It now has motion and camera interaction. In this chapter, we will expand the visual vocabulary of the scene by introducing more geometry types and a more expressive material system. We will also discuss why geometry and materials are separate concepts and how a mesh combines them into a renderable object.

This chapter continues to use the same HTML project file so the course remains a single evolving application.

## 4.1 What geometry is

### What it is

Geometry is the shape of a 3D object. It describes the vertices and faces that make up that shape.

### Why it exists

The GPU needs a mathematical description of a form before it can render it. Geometry provides that description.

### How it works internally

Three.js geometry objects store vertices, normals, UVs, and sometimes index data. The renderer sends this data to the GPU for drawing.

### When to use it

Use geometry whenever you need a shape, from a cube to a terrain mesh or a character body.

### Common mistakes

A common beginner mistake is to expect a geometry to include color or texture by itself. Geometry only defines structure.

### Performance considerations

Complex geometry can become expensive. Large numbers of vertices slow down rendering and memory use.

### Real-world uses

Geometry is used for characters, environments, vehicles, tools, terrains, and interfaces.

### Step-by-step implementation

We will create multiple shapes and combine them into the scene. We will also explain how the same material can be reused across different geometries.

### Beginner explanation

Geometry is the skeleton of the object.

### Intermediate explanation

It defines the surface structure of the object.

### Advanced explanation

Geometry is the vertex stream that the GPU consumes as triangle data.

## 4.2 BufferGeometry

### What it is

BufferGeometry is the low-level geometry representation used by Three.js for efficient vertex data storage.

### Why it exists

It allows the engine to work with arrays of positions, normals, colors, and UVs in a memory-efficient format.

### How it works internally

BufferGeometry stores data in typed arrays, which are compact and fast for the GPU to consume.

### When to use it

Use it when you need custom geometry or want to optimize performance.

### Common mistakes

A beginner may create a BufferGeometry incorrectly by forgetting to define the correct attribute layout.

### Performance considerations

BufferGeometry is efficient, but custom geometry requires careful setup and debugging.

### Real-world uses

It is used for procedural meshes, particles, terrain generation, and custom effects.

### Example

```javascript
const geometry = new THREE.BufferGeometry();

const vertices = new Float32Array([
  -1, -1, 0,
   1, -1, 0,
   0,  1, 0
]);

geometry.setAttribute('position', new THREE.BufferAttribute(vertices, 3));
```

### Beginner explanation

BufferGeometry is a more direct way to describe the shape.

### Intermediate explanation

It stores the shape data in arrays that the renderer can upload quickly.

### Advanced explanation

It is the core geometry abstraction that powers most of Three.js.

## 4.3 Materials

### What they are

Materials define how a surface looks. They control color, reflectivity, shininess, transparency, and other visual properties.

### Why they exist

A geometry can describe a shape, but the material describes its appearance.

### How they work internally

Materials contain shader information and parameters that determine how the surface is rendered.

### When to use them

Apply a material to every mesh for which you want a particular appearance.

### Common mistakes

A common mistake is expecting a material to change the geometry itself. It does not.

### Performance considerations

Materials that use complex shaders can be expensive. Simple materials are faster.

### Real-world uses

Materials are used everywhere: metals, plastics, glass, wood, skin, cloth, and more.

### Types of materials

Three.js offers many materials, including:

- MeshBasicMaterial
- MeshNormalMaterial
- MeshLambertMaterial
- MeshPhongMaterial
- MeshStandardMaterial
- MeshPhysicalMaterial

## 4.4 Meshes

### What they are

A mesh is the object that combines geometry and material into a drawable entity.

### Why they exist

The engine needs something that can be added to the scene and transformed in space. The mesh is that object.

### How they work internally

The mesh holds a reference to geometry and material and participates in the scene graph.

### When to use them

Use a mesh for any visible 3D object.

### Common mistakes

A beginner may forget to add the mesh to the scene or to use a material that is compatible with the selected rendering approach.

### Performance considerations

Sharing geometry and material between many meshes can save memory, but each mesh still has its own transform.

### Real-world uses

Meshes are used for characters, furniture, collectibles, props, and buildings.

## 4.5 Cube creation

The cube is still useful because it teaches the most basic geometry concept. We already used BoxGeometry. A box is made from faces that are triangles under the hood.

```javascript
const cubeGeometry = new THREE.BoxGeometry(1, 1, 1);
const cubeMaterial = new THREE.MeshStandardMaterial({ color: 0x38bdf8 });
const cube = new THREE.Mesh(cubeGeometry, cubeMaterial);
scene.add(cube);
```

### Beginner explanation

A cube is a box made with geometry and a material.

### Intermediate explanation

The cube is a mesh built from triangles that represent its faces.

### Advanced explanation

The cube geometry is converted to vertices and triangles for GPU rendering.

## 4.6 Sphere creation

### What it is

A sphere is a rounded geometry built from many segments.

### Why it exists

Spheres are common in games, planets, lights, particles, and props.

### How it works internally

SphereGeometry uses latitudes and longitudes to create a series of triangles around a center.

### When to use it

Use it for globes, projectiles, buttons, or decorative objects.

### Common mistakes

A beginner may choose too few segments and get a rough, faceted sphere.

### Performance considerations

More segments mean a smoother shape but more vertices.

### Example

```javascript
const sphere = new THREE.Mesh(
  new THREE.SphereGeometry(1, 32, 32),
  new THREE.MeshStandardMaterial({ color: 0xff7a59 })
);

sphere.position.set(2, 0, 0);
scene.add(sphere);
```

## 4.7 Plane creation

### What it is

A plane is a flat surface.

### Why it exists

Planes are used for floors, walls, backdrops, and simple surfaces.

### How it works internally

PlaneGeometry creates a quad with two triangles.

### When to use it

Use it for ground, walls, cutouts, and image-based surfaces.

### Common mistakes

A beginner often forgets that a plane has no thickness and is seen from one side unless double-sided material is used.

### Example

```javascript
const plane = new THREE.Mesh(
  new THREE.PlaneGeometry(10, 10),
  new THREE.MeshStandardMaterial({ color: 0x1f2937, side: THREE.DoubleSide })
);

plane.rotation.x = -Math.PI / 2;
scene.add(plane);
```

## 4.8 Cylinder and torus

### Cylinder

A CylinderGeometry creates a tube-like shape with a circular top and bottom. It is used for barrels, columns, wheels, and handles.

```javascript
const cylinder = new THREE.Mesh(
  new THREE.CylinderGeometry(0.4, 0.4, 1.5, 24),
  new THREE.MeshStandardMaterial({ color: 0x22c55e })
);

cylinder.position.set(-2, 0.75, 0);
scene.add(cylinder);
```

### Torus

A TorusGeometry creates a donut-like shape. It is useful for rings, decorative elements, and technical objects.

```javascript
const torus = new THREE.Mesh(
  new THREE.TorusGeometry(1, 0.3, 16, 100),
  new THREE.MeshStandardMaterial({ color: 0xf59e0b })
);

torus.position.set(0, 1.5, -2);
scene.add(torus);
```

## 4.9 Materials in more depth

### MeshBasicMaterial

A simple material that is not affected by lighting. It is great for debugging and flat color objects.

### MeshStandardMaterial

A physically inspired material that can respond to lights and roughness values. It is useful for realistic-looking surfaces.

### MeshPhysicalMaterial

A more advanced physically based material with features like clearcoat and transmission.

### MeshNormalMaterial

Helps visualize normals by encoding direction as color.

### Common mistakes

Beginner projects often use MeshBasicMaterial for everything, which can make the scene look flat even when lights are present.

## 4.10 Updating the project with multiple shapes

We can update the scene to include a ground plane, a sphere, a cylinder, and a torus around the animated cube. That gives the project more visual structure.

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

  const ambient = new THREE.AmbientLight(0xffffff, 0.4);
  scene.add(ambient);

  const directional = new THREE.DirectionalLight(0xffffff, 1.2);
  directional.position.set(3, 5, 4);
  scene.add(directional);

  const plane = new THREE.Mesh(
    new THREE.PlaneGeometry(12, 12),
    new THREE.MeshStandardMaterial({ color: 0x1f2937, side: THREE.DoubleSide })
  );
  plane.rotation.x = -Math.PI / 2;
  scene.add(plane);

  const cube = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshStandardMaterial({ color: 0x38bdf8 })
  );
  cube.position.set(0, 1, 0);
  scene.add(cube);

  const sphere = new THREE.Mesh(
    new THREE.SphereGeometry(0.75, 32, 32),
    new THREE.MeshStandardMaterial({ color: 0xff7a59 })
  );
  sphere.position.set(2.2, 0.75, 0);
  scene.add(sphere);

  const cylinder = new THREE.Mesh(
    new THREE.CylinderGeometry(0.4, 0.4, 1.5, 24),
    new THREE.MeshStandardMaterial({ color: 0x22c55e })
  );
  cylinder.position.set(-2.2, 0.75, 0);
  scene.add(cylinder);

  const torus = new THREE.Mesh(
    new THREE.TorusGeometry(0.8, 0.25, 16, 100),
    new THREE.MeshStandardMaterial({ color: 0xf59e0b })
  );
  torus.position.set(0, 1.2, -2.2);
  scene.add(torus);

  const clock = new THREE.Clock();

  function animate() {
    requestAnimationFrame(animate);
    const elapsed = clock.getElapsedTime();

    cube.rotation.y = elapsed;
    sphere.rotation.y = elapsed * 1.2;
    cylinder.rotation.z = elapsed * 0.8;
    torus.rotation.x = elapsed * 0.7;

    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- The scene receives a background color and lights.
- A plane becomes the floor.
- Several meshes are created with different geometry and materials.
- Each object is positioned and animated.
- The controls allow interactive camera manipulation.

## 4.11 Mini project: create a small diorama

Build a tiny landscape with a plane, a few rocks, and a simple tower. Use different materials so the scene feels varied.

## 4.12 Challenge problem

Create a procedural archway by combining a plane, cylinders, and torus geometry. Make it look like a gate or doorway.

## 4.13 Troubleshooting

### Problem: The shapes are invisible

Check whether the object has been added to the scene and whether the camera is pointed toward it.

### Problem: The materials look flat

Use MeshStandardMaterial or MeshPhysicalMaterial and add lights.

### Problem: The geometry looks faceted

Increase the segment count for smoother curves.

## 4.14 Summary

In this chapter, you learned how geometry defines shape, how materials define appearance, and how meshes combine them into visible objects. You also explored common primitives such as cubes, spheres, planes, cylinders, and toruses. The project now includes a richer environment made from multiple shapes and materials.

The next chapter will introduce lights and shadows, which make the environment feel much more real.
