# Chapter 12 — AnimationMixer, Skeletal Animation, and Imported Motion

The project has moved from procedural objects to imported content. In this chapter, we add motion to those assets. A large part of modern 3D applications is not just displaying models, but also playing animations: characters run, doors open, machines move, and objects react to the world.

We continue evolving the same project file in [course/project.html](course/project.html) so the course remains one continuous project.

## 12.1 What animation is in 3D

### What it is

Animation is the changing of an object’s properties over time. In 3D, this often means moving joints, changing bones, deforming meshes, or altering transforms.

### Why it exists

Without animation, models would remain static. Animation gives life and behavior to characters, vehicles, machines, and environmental elements.

### How it works internally

The engine evaluates keyframes or data-driven transformations over time and applies them to the scene graph or skeleton.

### When to use it

Use animation for characters, UI transitions, object motion, interactive responses, and cutscenes.

### Common mistakes

A beginner might assume that a GLTF file will play automatically without the proper mixer setup.

### Performance considerations

Animation is usually not too expensive, but complex skeletal systems and many animated objects can add cost.

### Real-world uses

Animation is used in games, product demos, digital humans, educational content, and interactive storytelling.

## 12.2 AnimationMixer

### What it is

AnimationMixer is the Three.js object that controls the playback of animations on a model.

### Why it exists

A model may contain many clips, and the mixer organizes them and updates them over time.

### How it works internally

The mixer keeps track of actions, their current time, their weights, and their playback state. It applies the resulting transforms to the model each frame.

### When to use it

Use it whenever you want to play one or more animations on a loaded model.

### Common mistakes

A beginner may forget to update the mixer each frame, causing the animation to stay frozen.

### Performance considerations

The mixer is lightweight, but many animated characters can be expensive.

### Example

```javascript
const mixer = new THREE.AnimationMixer(model);
const action = mixer.clipAction(clip);
action.play();
```

## 12.3 Skeletal animation

### What it is

Skeletal animation is animation driven by a skeleton of bones or joints that deform a mesh.

### Why it exists

It is efficient and flexible for character motion.

### How it works internally

The skeleton stores bone transforms. The mesh vertices are weighted to nearby bones. When the bones move, the vertices are updated accordingly.

### When to use it

Use it for characters, creatures, and articulated objects.

### Common mistakes

A beginner might expect every model to have skinning data. Some models do not.

### Performance considerations

Skeletal animation is widely used because it is efficient, but complex rigs can still cost more than simple object transforms.

## 12.4 Updating the project with an animated model

We will add an animated model loader and update the mixer each frame.

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

  const ambient = new THREE.AmbientLight(0xffffff, 0.5);
  scene.add(ambient);

  const directional = new THREE.DirectionalLight(0xffffff, 1.2);
  directional.position.set(3, 5, 4);
  scene.add(directional);

  let mixer = null;

  const loader = new GLTFLoader();
  loader.load(
    'https://threejs.org/examples/models/gltf/RobotExpressive/RobotExpressive.glb',
    (gltf) => {
      const model = gltf.scene;
      model.scale.set(0.8, 0.8, 0.8);
      model.position.set(0, 0, 0);
      scene.add(model);

      mixer = new THREE.AnimationMixer(model);
      const clip = gltf.animations[0];
      if (clip) {
        const action = mixer.clipAction(clip);
        action.play();
      }
    },
    undefined,
    (error) => {
      console.error('Animation model failed to load:', error);
    }
  );

  const clock = new THREE.Clock();

  function animate() {
    requestAnimationFrame(animate);
    const delta = clock.getDelta();
    if (mixer) mixer.update(delta);
    controls.update();
    renderer.render(scene, camera);
  }

  animate();
</script>
```

### Line-by-line explanation

- The GLTFLoader loads an animated model.
- The model is added to the scene when it finishes loading.
- An AnimationMixer is created for that model.
- The mixer is updated every frame with delta time.

## 12.5 Mini project: create a character scene

Load an animated character and place it in front of a simple environment. Add a floor, some lights, and a small camera orbit.

## 12.6 Challenge problem

Make two actions play on the same mixer, such as idle and wave, and blend them smoothly with weights.

## 12.7 Troubleshooting

### Problem: The model appears but does not animate

Check whether the model actually contains animations and whether the mixer is being updated.

### Problem: The animation is too fast or too slow

Use delta time and adjust the playback speed if necessary.

## 12.8 Summary

In this chapter, you learned about AnimationMixer and skeletal animation. These systems let imported models move in believable ways and are essential for character-driven and interactive experiences.

The next chapter will introduce audio, including positional audio and sound in 3D space.
