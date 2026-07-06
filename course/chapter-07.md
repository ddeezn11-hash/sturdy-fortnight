# Chapter 7 — Raycasting, Selection, and Object Interaction

The project is now interactive in a basic way: it responds to time and keyboard input. In this chapter, we make the scene more expressive by adding object interaction. This is a key step in moving from a simple visual scene to a real application that can respond to the user’s intent.

The same growing HTML project remains the foundation. We will continue modifying [course/project.html](course/project.html) so the course stays consistent and practical.

## 7.1 What raycasting is

### What it is

Raycasting is the process of sending an invisible line, or ray, into the scene to detect intersections with objects.

### Why it exists

It is one of the most common ways to answer questions such as:

- what object is under the mouse?
- did a bullet hit something?
- where is the player looking?
- should this action be triggered?

### How it works internally

A ray is defined by an origin point and a direction. The engine tests whether the ray intersects any geometry in the scene. If it does, the hit point and the intersected object are returned.

### When to use it

Use raycasting for object selection, aiming, mouse picking, line-of-sight checks, firing weapons, and interaction systems.

### Common mistakes

A beginner often forgets that raycasting requires a camera and a pointer position. It is not just a simple line in space.

### Performance considerations

Raycasting is usually cheap for a small number of objects, but it can become expensive if you test many rays against complex scenes each frame.

### Real-world uses

Raycasting is used in games for aiming, selection, hit detection, and AI line-of-sight.

### Step-by-step implementation

We will:

1. create a Raycaster
2. convert the mouse position into normalized device coordinates
3. update the ray each frame or on mouse movement
4. test it against scene objects
5. highlight an object when it is selected

### Beginner explanation

A ray is like an invisible laser beam that can detect what it touches.

### Intermediate explanation

Raycasting maps the pointer into the 3D scene and checks for intersections.

### Advanced explanation

The ray is tested against geometry in world space using intersection math and the scene graph.

## 7.2 Raycaster

### What it is

Raycaster is the Three.js class that performs ray intersection tests.

### Why it exists

It provides a high-level interface for ray-based interactions.

### How it works internally

The raycaster stores a ray and a set of objects to test against. It uses the camera and the pointer position to build the ray and then checks intersections.

### When to use it

Use it when you want to know whether a point or direction intersects an object.

### Common mistakes

A beginner may forget to use the renderer’s canvas size or the right coordinate system.

### Performance considerations

It is efficient for interactive selection, but it should be used carefully in large scenes.

### Example

```javascript
const raycaster = new THREE.Raycaster();
const pointer = new THREE.Vector2();

function onPointerMove(event) {
  pointer.x = (event.clientX / window.innerWidth) * 2 - 1;
  pointer.y = -(event.clientY / window.innerHeight) * 2 + 1;
}

window.addEventListener('pointermove', onPointerMove);
```

## 7.3 Mouse picking

### What it is

Mouse picking is the process of detecting which object the mouse is pointing at in the 3D scene.

### Why it exists

It is the basis for object selection, interaction, and UI systems.

### How it works internally

The pointer coordinates are transformed into a ray in world space, and the ray is tested against objects in the scene.

### When to use it

Use it in editors, games, product viewers, and exploration scenes.

### Common mistakes

A beginner might ignore the camera’s projection and accidentally cast the ray from the wrong coordinates.

### Performance considerations

This is inexpensive for a moderate number of objects, but can become expensive with thousands of meshes.

### Example

```javascript
raycaster.setFromCamera(pointer, camera);

const intersects = raycaster.intersectObjects(scene.children, true);

if (intersects.length > 0) {
  console.log('Selected object:', intersects[0].object.name);
}
```

## 7.4 Object selection

### What it is

Selection means highlighting or marking the object that the user is interacting with.

### Why it exists

It gives users feedback about what they are targeting.

### How it works internally

When an object is intersected, the application stores it and updates its appearance or state.

### When to use it

Use it for clickable objects, inventory systems, and editor tools.

### Common mistakes

A beginner might forget to clear the previous selection and end up with multiple highlighted objects.

### Performance considerations

Highlighting a single object is cheap. Highlighting many objects repeatedly can cost more.

### Implementation idea

```javascript
let selectedObject = null;

function updateSelection() {
  raycaster.setFromCamera(pointer, camera);
  const intersects = raycaster.intersectObjects(scene.children, true);

  if (intersects.length > 0) {
    const object = intersects[0].object;

    if (selectedObject !== object) {
      if (selectedObject) {
        selectedObject.material.emissive.setHex(0x000000);
      }
      selectedObject = object;
      selectedObject.material.emissive.setHex(0x0044ff);
    }
  } else if (selectedObject) {
    selectedObject.material.emissive.setHex(0x000000);
    selectedObject = null;
  }
}
```

## 7.5 Shooting rays

### What it is

Shooting a ray means using the raycasting system as a weapon or tool to test whether something was hit.

### Why it exists

It is a symbolic and practical foundation for shooting games, lasers, and spells.

### How it works internally

A ray is created from the character’s position or the camera and then tested against the scene.

### When to use it

Use it in shooters, spell-casting systems, and target acquisition.

### Common mistakes

A beginner might cast the ray from the wrong point, making the shot feel inaccurate.

### Performance considerations

This is efficient for small numbers of interactions, especially when done on demand rather than every frame.

## 7.6 Collision detection basics

### What it is

Collision detection is the process of checking whether two objects overlap or intersect.

### Why it exists

It is necessary for preventing movement through walls, triggering events, and detecting hits.

### How it works internally

The engine uses shapes and bounds to estimate whether objects meet. The simplest form is a bounding box or bounding sphere test.

### When to use it

Use it when moving characters or objects through a scene.

### Common mistakes

A beginner may assume that collision detection is perfect. In many cases, it is an approximation.

### Performance considerations

Simple bounding volumes are efficient; more precise collision geometry is more expensive.

## 7.7 Bounding boxes

### What they are

Bounding boxes are axis-aligned boxes that enclose an object.

### Why they exist

They are a fast and simple way to approximate an object’s volume.

### How they work internally

The box is derived from an object’s extents in world space.

### When to use them

Use them for broad-phase tests and simple physics interactions.

### Common mistakes

A beginner may use a bounding box that is too large or too small, leading to poor collision results.

### Example

```javascript
const box3 = new THREE.Box3().setFromObject(cube);
console.log(box3);
```

## 7.8 Bounding spheres

### What they are

Bounding spheres are sphere-shaped volumes that envelop an object.

### Why they exist

They are even cheaper than boxes for some tests and easy to compute.

### How they work internally

A sphere is defined by a center and a radius.

### When to use them

Use them for fast broad-phase tests or simple object containment checks.

### Common mistakes

A beginner may choose a radius that does not fit the object well.

## 7.9 Frustum culling

### What it is

Frustum culling is the process of not rendering objects that lie outside the camera’s viewing volume.

### Why it exists

It saves GPU work by avoiding unnecessary rendering.

### How it works internally

The camera’s frustum is tested against the object’s bounds. Objects outside the frustum are skipped.

### When to use it

Use it in large scenes and games with many objects.

### Common mistakes

A beginner might assume culling happens automatically for every object, but it depends on the engine and the object’s bounds.

### Performance considerations

It is a powerful optimization, especially in complex worlds.

## 7.10 Updating the project with clickable objects

We will extend the project so that the user can hover over and click objects. The selected object will change color to provide feedback.

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

  const raycaster = new THREE.Raycaster();
  const pointer = new THREE.Vector2();
  let selectedObject = null;

  const cube = new THREE.Mesh(
    new THREE.BoxGeometry(1, 1, 1),
    new THREE.MeshStandardMaterial({ color: 0x38bdf8 })
  );
  cube.name = 'Cube';
  cube.position.set(0, 1, 0);
  scene.add(cube);

  const sphere = new THREE.Mesh(
    new THREE.SphereGeometry(0.8, 24, 24),
    new THREE.MeshStandardMaterial({ color: 0xff7a59 })
  );
  sphere.name = 'Sphere';
  sphere.position.set(2, 1, 0);
  scene.add(sphere);

  const ambient = new THREE.AmbientLight(0xffffff, 0.3);
  scene.add(ambient);

  const directional = new THREE.DirectionalLight(0xffffff, 1.2);
  directional.position.set(3, 5, 4);
  scene.add(directional);

  function updateSelection() {
    raycaster.setFromCamera(pointer, camera);
    const intersects = raycaster.intersectObjects(scene.children, true);

    if (intersects.length > 0) {
      const object = intersects[0].object;
      if (selectedObject !== object) {
        if (selectedObject) {
          selectedObject.material.emissive.setHex(0x000000);
        }
        selectedObject = object;
        selectedObject.material.emissive.setHex(0x0044ff);
      }
    } else if (selectedObject) {
      selectedObject.material.emissive.setHex(0x000000);
      selectedObject = null;
    }
  }

  function onPointerMove(event) {
    pointer.x = (event.clientX / window.innerWidth) * 2 - 1;
    pointer.y = -(event.clientY / window.innerHeight) * 2 + 1;
    updateSelection();
  }

  window.addEventListener('pointermove', onPointerMove);

  function animate() {
    requestAnimationFrame(animate);
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- A Raycaster and pointer vector are created.
- The pointer position is normalized using the viewport size.
- On every movement, the ray is updated and used to select an object.
- When an object is hit, its emissive color is changed to show selection.

## 7.11 Mini project: create a clickable inventory object

Make several objects appear in the scene and allow the user to click them to display their names in a small overlay.

## 7.12 Challenge problem

Create a simple shooting mechanic where a ray is shot from the camera and hits a target object, changing its color when hit.

## 7.13 Troubleshooting

### Problem: Nothing is selected

Make sure the raycaster is updated after the pointer moves and that the objects are in the scene.

### Problem: The selection is unstable

The object’s material may be shared. Check whether multiple objects share the same material instance.

### Problem: The ray seems to come from the wrong place

Review the camera position and the normalization of the pointer coordinates.

## 7.14 Summary

In this chapter, you learned about raycasting, mouse picking, object selection, shooting rays, collision detection, bounding boxes, bounding spheres, and frustum culling. These concepts connect input, interaction, and scene understanding and form the basis of many interactive 3D applications.

The next chapter will introduce textures, UV mapping, and surface detail so the objects become visually richer and less flat.
