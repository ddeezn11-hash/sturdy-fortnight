# Chapter 2 — Transformations and Scene Hierarchy

The first chapter introduced the basic idea of a Three.js scene: a renderer, a scene, a camera, and a mesh. In this chapter, the project becomes less static and more dynamic. We will make the cube move, rotate, and scale in ways that are controlled by mathematics. We will also introduce the concept of object hierarchy, which is one of the most important ideas in 3D engines.

The project in [course/project.html](course/project.html) will continue to be the same file that we edit, but the content will become more expressive. We will still keep the single-file workflow for now. That way, you can see the project evolve from a simple cube to a richer scene without changing tools or starting over.

## 2.1 What transformations are

### What they are

Transformations are the ways an object changes in space. In Three.js, every object can be moved, rotated, and scaled.

### Why they exist

A 3D engine needs a way to describe where objects are, how they are oriented, and how large they are. Transformations provide this vocabulary.

### How they work internally

Each Object3D stores a position, a quaternion or Euler rotation, and a scale. Three.js combines these into a matrix that is used to transform vertices from object space into world space and then into camera space.

### When to use them

Use transformations whenever you need an object to move, turn, or change size.

### Common mistakes

A beginner often confuses local space and world space. A child object may appear to move differently from a parent object if the parent is rotated or translated.

### Performance considerations

Transformations are cheap compared with heavy shaders or high-resolution textures, but updating many objects every frame can still cost time.

### Real-world uses

Transformations are used in games for characters, cameras, vehicles, weapons, UI, props, and environmental objects.

### Step-by-step implementation

We will update the project so that the cube uses:

- position for movement
- rotation for spinning
- scale for resizing

We will then place the cube inside a group so we can manipulate the whole group as one unit.

### Beginner explanation

Transformations tell the computer where an object is and how it is oriented.

### Intermediate explanation

An object can be transformed in local coordinates and then placed into a larger scene graph.

### Advanced explanation

Transformations are represented by matrices, and the scene graph uses hierarchical transforms to compute final positions efficiently.

## 2.2 Position, rotation, and scale

Every Mesh inherits from Object3D, which provides three fundamental properties:

- position
- rotation
- scale

They are the most important tools for movement.

### Position

Position is a Vector3. It represents the object’s translation in 3D space.

```javascript
cube.position.x = 1;
cube.position.y = 0.5;
cube.position.z = -2;
```

The coordinates are relative to the parent object or the world origin if the object has no parent.

### Rotation

Rotation controls the object’s orientation. We can set it using Euler angles or quaternions.

```javascript
cube.rotation.x = 0.4;
cube.rotation.y = 0.6;
cube.rotation.z = 0.2;
```

### Scale

Scale changes the size of the object.

```javascript
cube.scale.set(1.2, 0.8, 1.5);
```

### Why each exists

- Position changes where the object is.
- Rotation changes how it faces.
- Scale changes how large it is.

### Common mistakes

Changing rotation values in degrees is a common beginner mistake. Three.js uses radians, not degrees.

### Performance note

Updating transforms every frame is ordinary. Doing heavy work inside the loop is the expensive part.

## 2.3 Euler rotations

### What they are

Euler rotations describe orientation with three angles: pitch, yaw, and roll. In Three.js, these are usually stored as x, y, and z rotations.

### Why they exist

They are intuitive and easy to understand. Many beginners learn them first because they resemble familiar coordinate axes.

### How they work internally

Euler angles are converted into a rotation matrix or a quaternion internally. They are convenient for humans but can create gimbal lock in some systems.

### When to use them

Use Euler rotations for readability, simple prototypes, or user-facing controls where the exact mathematical subtleties are not critical.

### Common mistakes

A major mistake is assuming Euler angles always behave predictably for arbitrary rotations. They can become ambiguous in some cases because of axis order.

### Advanced note

Three.js uses a default order of XYZ. If you are making more complex rotation systems, quaternions are often preferable.

### Implementation example

```javascript
cube.rotation.order = 'XYZ';
cube.rotation.x = Math.PI / 4;
cube.rotation.y = Math.PI / 6;
cube.rotation.z = 0.1;
```

### Beginner explanation

Euler rotations are like turning an object around its three axes.

### Intermediate explanation

They are simple but can become unstable when you compose many complex rotations.

### Advanced explanation

Euler angles are compact but can suffer from singularities and ambiguity due to their representation.

## 2.4 Quaternions

### What they are

Quaternions are a mathematically elegant way to represent rotation.

### Why they exist

They avoid the limitations of Euler angles and are preferred in many real-time graphics systems.

### How they work internally

A quaternion stores a rotation as a compact four-component representation that is robust for interpolation and composition.

### When to use them

Use quaternions for smooth camera motion, character animation, physics integration, or any system that needs stable rotational math.

### Common mistakes

A beginner might try to directly manipulate quaternion components without understanding that they are not as intuitive as Euler angles.

### Performance considerations

They are efficient and widely used, but you usually do not need to reason about them manually unless you are building advanced systems.

### Real-world uses

Quaternions are standard in games, animation systems, and robotics simulations.

### Example

```javascript
const quaternion = new THREE.Quaternion();
quaternion.setFromAxisAngle(new THREE.Vector3(0, 1, 0), Math.PI / 4);
cube.quaternion.copy(quaternion);
```

### Beginner explanation

Quaternions are a smarter way to describe rotation.

### Intermediate explanation

They are useful because they avoid some of the problems that appear with Euler angles.

### Advanced explanation

They support smooth interpolation and stable composition, making them ideal for animation and physics.

## 2.5 Matrix transforms

### What they are

Matrices are the internal representation of transformations.

### Why they exist

The GPU needs a compact, efficient structure to transform vertices. Matrices provide that structure.

### How they work internally

A matrix can encode translation, rotation, and scale into one object. Three.js uses matrices internally to convert object space into world space and then into view space.

### When to use them

You normally do not create matrices manually in beginner projects. However, understanding them helps when you debug unusual transformations or implement custom systems.

### Common mistakes

A common mistake is mixing up row-major and column-major conventions or forgetting that transformations are applied in a particular order.

### Performance considerations

Matrices are highly optimized for graphics hardware. That is why the engine uses them heavily.

### Real-world uses

Matrices are used in rendering, camera projection, animation skinning, and physics.

### A simple formula

A transform matrix can be thought of as:

$$
M = T \cdot R \cdot S
$$

This formula means: first scale, then rotate, then translate. The order matters.

### Beginner explanation

Matrices are the hidden machinery behind movement and rotation.

### Intermediate explanation

Matrices let the engine apply combined transformations in a compact form.

### Advanced explanation

The engine uses matrices to transform vertices in hardware, and the order of multiplication affects the result.

## 2.6 Object3D and the scene graph

### What it is

Object3D is the base class for most 3D objects in Three.js, including Mesh, Group, Camera, and Light.

### Why it exists

It provides the common features that all 3D objects need: transform properties, parenting, and scene graph membership.

### How it works internally

An Object3D can have a parent and children. The child’s transform is computed relative to its parent.

### When to use it

Use it when you want to group objects or build a hierarchy.

### Common mistakes

New developers sometimes forget that a child object’s position is relative to the parent, not the world origin.

### Performance considerations

Hierarchies are efficient and useful, but deep hierarchies should be kept manageable.

### Real-world uses

Characters, vehicles, robots, and furniture are often modeled as hierarchies of parts.

### Example

```javascript
const parent = new THREE.Group();
parent.position.set(0, 0, 0);

const child = new THREE.Mesh(geometry, material);
child.position.set(1, 0, 0);

parent.add(child);
scene.add(parent);
```

The child will appear one unit away from the parent.

## 2.7 Groups and object hierarchy

### What they are

Groups are containers for objects. They let you treat many objects as a single transformable unit.

### Why they exist

When you build a complex scene, you often want to move or rotate several objects together.

### How they work internally

A Group is an Object3D with children. The group’s transform is applied to all children.

### When to use them

Use groups for robots, buildings, vehicles, weapons, or collections of props.

### Common mistakes

A beginner might add the same child to two parents, which is not valid for a single scene graph.

### Performance considerations

Groups are efficient, but too many nested groups can make debugging more complex.

### Real-world uses

Scene graphs are used in games, CAD tools, and simulation software.

### Implementation idea

```javascript
const vehicleGroup = new THREE.Group();
vehicleGroup.position.set(0, 0, -3);

const body = new THREE.Mesh(new THREE.BoxGeometry(2, 1, 1), material);
const wheel = new THREE.Mesh(new THREE.CylinderGeometry(0.3, 0.3, 0.2, 16), material);

vehicleGroup.add(body);
vehicleGroup.add(wheel);
scene.add(vehicleGroup);
```

This creates a hierarchy that can be moved together.

## 2.8 Updating the project file

We will extend the current project with a small animated transformation system. The cube will:

- wobble in position
- spin in rotation
- pulse in scale

The code will use a single render loop, and each frame will update the transforms.

```html
<script type="module">
  import * as THREE from 'three';

  const renderer = new THREE.WebGLRenderer({ antialias: true });
  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
  document.body.appendChild(renderer.domElement);

  const scene = new THREE.Scene();
  scene.background = new THREE.Color(0x0f172a);

  const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 100);
  camera.position.set(0, 0, 6);

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

    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- The script begins with imports and renderer setup.
- The scene and camera are created as before.
- A Group is created so that several objects can be transformed together.
- The cube is added to the group.
- A Clock instance measures time.
- In the animation loop, elapsed time is used to update the group’s position, rotation, and scale.
- The renderer draws the updated scene.

## 2.9 Mini project: create a tiny spaceship

Use a group to represent a spaceship. Put a body, wings, and a cockpit inside it. Then animate the whole group in the scene.

### Goals

- practice hierarchy
- practice transforms
- practice frame-based animation

## 2.10 Challenge problem

Create a simple solar system with a sun and orbiting planets. Each planet should be a child of a group, and the group should rotate around the sun while the planet also rotates on its own axis.

## 2.11 Troubleshooting

### Problem: The object seems to move strangely

The object may be attached to a parent group with a transform applied to it.

### Problem: Rotation looks wrong

You may be using radians incorrectly or applying rotations in an order that is not what you expect.

### Problem: The scale feels inconsistent

Remember that scale multiplies the object dimensions from its local origin.

## 2.12 Summary

In this chapter, you learned how objects change in space through transformations. You explored position, rotation, scale, Euler angles, quaternions, matrix transforms, Object3D, and groups. You also saw how a scene graph allows parents and children to work together so that complex assemblies can be manipulated as a unit.

This chapter gives you the vocabulary needed for all later chapters. Once you are ready, the next chapter will expand the same project by introducing cameras and camera movement in greater depth.
