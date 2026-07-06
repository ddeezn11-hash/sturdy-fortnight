# Chapter 1 — Your First Three.js Scene

Welcome to the first chapter of this course. In this chapter, you will build the foundation of the project that will grow across the entire book. We will begin with a single HTML file and make it progressively richer. By the end of this chapter, you will have a working Three.js scene that displays a rotating cube on the screen.

This course is intentionally structured as one long project rather than many isolated demos. Every chapter will modify the same file, which is located at [course/project.html](course/project.html). That means you will not throw away your work after each lesson. Instead, your project will evolve into a polished, interactive 3D application.

The goal of this chapter is to make sure you understand the core mental model of Three.js before we move on to advanced features such as lighting, cameras, physics concepts, particles, shaders, and loading assets.

## 1.1 The course project: one file, one growing application

The project for this course will be a single interactive 3D world that begins as a tiny rotating cube and later expands into a richer environment. We will keep everything in one HTML file at first because that makes the learning path easier. Later, once the project is large enough, we will discuss how to split it into multiple files without losing the structure.

At this stage, the project will do only a few things:

- create a canvas for drawing 3D graphics
- create a 3D scene
- place a camera in the scene
- create a cube
- animate the cube
- render the scene each frame

That seems simple, but it introduces the most important Three.js ideas:

- renderer
- scene
- camera
- geometry
- material
- mesh
- animation loop

You can think of Three.js as a system that turns a set of 3D objects and instructions into pixels on the screen.

## 1.2 What Three.js is

### What it is

Three.js is a JavaScript library for creating 3D graphics in the browser. It gives you a high-level API for working with 3D objects, cameras, lights, materials, textures, animations, and many other features. It sits on top of WebGL and hides much of the low-level complexity.

### Why it exists

Without Three.js, working directly with WebGL would require writing a lot of low-level code. A typical WebGL program needs:

- shader code
- buffer management
- matrix calculations
- state setup
- attribute handling
- draw calls

Three.js makes those tasks easier by providing classes and helpers. It is used for:

- interactive product viewers
- games
- data visualizations
- scientific simulations
- AR and VR experiences
- architectural walkthroughs

### How it works internally

Three.js is not magic. It uses JavaScript objects to describe a scene. Those objects are converted into data that WebGL understands. Then WebGL sends that data to the GPU, which draws it. The important idea is this:

- you describe the scene in JavaScript
- Three.js prepares the data
- WebGL sends it to the GPU
- the GPU rasterizes the geometry into pixels

### When to use it

Use Three.js when you want to create interactive 3D content in a web browser without needing to build everything from scratch. It is a great fit when you need:

- browser-based 3D experiences
- quick prototyping
- a modern, cross-platform runtime
- integration with HTML, CSS, and JavaScript

### Common mistakes

A beginner often assumes that Three.js will automatically make everything efficient. It will not. New users also commonly forget that:

- a camera must be added to the render pipeline in a meaningful way
- the object must be added to the scene
- the renderer must be appended to the page
- the animation loop must continue running
- the scene must be re-rendered each frame

### Performance considerations

Three.js is powerful, but it can become expensive if you use too many objects, too many lights, or too many complex textures. For beginners, the biggest performance problems usually come from:

- too many draw calls
- excessive overdraw
- large textures
- unnecessary resizing
- expensive shaders

### Real-world uses

Three.js is used in:

- online product configurators
- museum exhibits
- game prototypes
- educational simulations
- architectural visualization
- data-driven 3D dashboards

### Step-by-step implementation

In this chapter, we will implement the smallest useful scene possible:

1. create an HTML page
2. add an import map
3. import Three.js as a module
4. create a renderer
5. create a scene
6. create a camera
7. create a cube geometry
8. create a material
9. create a mesh
10. add the mesh to the scene
11. animate it with requestAnimationFrame
12. render it

### Beginner explanation

Three.js helps you draw 3D objects in a web page. You can think of it as a toolkit that takes your 3D shapes and sends them to the browser for display.

### Intermediate explanation

Three.js abstracts away much of the WebGL boilerplate so that developers can work with semantic concepts like Scene, Camera, Mesh, and Material.

### Advanced explanation

Three.js sits between your application code and the GPU. It manages resources, scene graphs, transformations, and draw operations while exposing a friendly API that still allows you to fine-tune many lower-level details.

## 1.3 How WebGL works

### What it is

WebGL is a JavaScript API that lets browsers access the GPU for rendering 2D and 3D graphics. It is the low-level graphics API that Three.js uses under the hood.

### Why it exists

The browser needs a standard way to ask the GPU to draw shapes, colors, and textures. WebGL provides that bridge. It is designed to let web applications use hardware acceleration without requiring a separate runtime.

### How it works internally

WebGL works with a graphics pipeline. The pipeline begins with vertex data and ends with pixels. The GPU processes this data in stages:

- vertex processing
- primitive assembly
- rasterization
- fragment processing
- final output to the framebuffer

You can imagine it this way:

```text
JavaScript / Three.js code
        |
        v
Scene description
        |
        v
Geometry + materials + transforms
        |
        v
WebGL buffers and shaders
        |
        v
GPU rendering pipeline
        |
        v
Pixels on the canvas
```

### When to use it

You usually do not use WebGL directly unless you are building very low-level graphics systems or custom shaders. Three.js is the easier path for most developers.

### Common mistakes

A common mistake is to think that WebGL automatically handles everything for you. In reality, you still need to manage:

- buffers
- shader programs
- transformations
- camera matrices
- state changes

### Performance considerations

WebGL performance depends heavily on how efficiently data is sent to the GPU. Large vertex buffers, frequent state changes, and expensive shader programs can all cause slowdowns.

### Real-world uses

WebGL powers many browser-based graphics applications, including:

- games
- maps with 3D layers
- CAD-style viewers
- medical visualizations
- data surfaces and charts

### Step-by-step implementation

We will not write raw WebGL in this course. Instead, we will rely on Three.js to generate the WebGL calls for us. Still, it is helpful to know that each Three.js operation maps to some WebGL work underneath.

### Beginner explanation

WebGL is the part of the browser that talks directly to the graphics card.

### Intermediate explanation

Three.js builds a scene graph and then converts it into the data structures that WebGL expects.

### Advanced explanation

WebGL exposes a programmable pipeline where shaders define how vertices and fragments are processed. Three.js wraps these concepts with higher-level abstractions.

## 1.4 The browser rendering pipeline

### What it is

The browser rendering pipeline is the sequence of steps that begins with your JavaScript code and ends with pixels on the screen.

### Why it exists

The browser must repeatedly process updates from your code, user input, layout changes, and paint operations. Its rendering pipeline makes sure this happens in a coordinated way.

### How it works internally

The browser has a main thread that handles JavaScript and event callbacks. It also has a rendering pipeline that includes style calculation, layout, paint, and composite steps. The GPU draws the final pixels after the browser has produced the relevant layers.

A simplified version looks like this:

```text
JavaScript event loop
        |
        v
Style calculation
        |
        v
Layout
        |
        v
Paint
        |
        v
Composite
        |
        v
Display
```

In a Three.js application, your animation loop usually runs inside the browser’s main thread and asks the renderer to draw a new frame.

### When to use it

You need to understand this pipeline because animations and interaction are tied to redraw timing. If you change the scene too often or block the main thread, your frame rate will drop.

### Common mistakes

A common beginner mistake is assuming that the browser will redraw automatically whenever you change a property. In reality, you must trigger the render process again, usually through the animation loop.

### Performance considerations

If your JavaScript runs for too long, the browser may miss the ideal frame timing. That causes stutter. In 3D work, this often happens when:

- the main thread is blocked by heavy computation
- there are too many DOM operations
- physics is computed on the main thread
- the scene is re-rendered unnecessarily

### Real-world uses

Understanding the browser rendering pipeline helps with:

- smooth animation
- responsive UI integration
- performance tuning
- debugging dropped frames

### Step-by-step implementation

In our project, the browser will call our animate function repeatedly. That function updates the cube and then calls the renderer to draw a new frame.

### Beginner explanation

The browser updates the screen many times per second. Three.js uses this timing to animate the scene.

### Intermediate explanation

The animation loop is tied to the browser’s repaint cycle. If you update the scene at the right time, the motion looks smooth.

### Advanced explanation

The frame rate is governed by the browser’s scheduling, device refresh rate, and rendering workload. Smooth 3D applications must respect this timing to avoid jank.

## 1.5 HTML setup

### What it is

The HTML file is the shell that hosts the canvas and any overlay UI elements. It also defines where the JavaScript is loaded from.

### Why it exists

The browser cannot display 3D content unless there is a page to host it. The HTML document provides the container, styles, and scripts required by the app.

### How it works internally

The browser parses the HTML and constructs the DOM. When it encounters the script tag, it loads and executes the JavaScript. In our case, the script is an ES module, which means it can use import syntax.

### When to use it

Always use an HTML page as the entry point for a Three.js app. It gives you a place to attach the renderer’s canvas and present UI overlays.

### Common mistakes

Beginners often forget to set the canvas to fill the viewport or to remove the default body margin. This can lead to strange layout issues.

### Performance considerations

Avoid unnecessary DOM elements inside the 3D scene. Keep the page simple so the browser does not spend time on heavy layout work.

### Real-world uses

A Three.js scene is usually part of a larger website or web app. The HTML setup is what connects the 3D world to the rest of the experience.

### Step-by-step implementation

Our HTML page includes:

- a full-screen body
- a canvas created by Three.js
- an import map for the Three.js module
- a module script for our application code

### Beginner explanation

The HTML file tells the browser what to show and where to load the 3D code.

### Intermediate explanation

The HTML page is the entry point for the app, and the renderer attaches its canvas to the DOM.

### Advanced explanation

A web-based 3D application is still a web application. The DOM, CSS, and JavaScript all interact together, and the canvas is just one part of the environment.

## 1.6 JavaScript modules and import maps

### What they are

JavaScript modules let you split code into files and import functions, classes, or values from one file to another. An import map is a browser feature that lets you define aliases for modules, such as mapping the short name three to a full CDN URL.

### Why they exist

Modules support better organization and avoid global namespace pollution. Import maps make module imports easier to read and maintain.

### How they work internally

The browser loads the module script and resolves its import statements. When it encounters an import map entry, it uses that mapping to find the correct file.

### When to use them

Use modules whenever you write modern JavaScript. Import maps are especially helpful when you want to keep imports clean.

### Common mistakes

A common mistake is forgetting to use the correct script type. A module script must use type="module". Another mistake is assuming the import map will work without the browser supporting it, though modern browsers do.

### Performance considerations

Modules can improve maintainability, but too many small imports may add overhead. For small beginner projects, the tradeoff is negligible.

### Real-world uses

Modules are standard in modern web development. They are used for everything from small widgets to large applications.

### Step-by-step implementation

In our file:

1. we define an import map with the alias three
2. we use an ES module script
3. we import Three.js using import * as THREE from 'three'

### Beginner explanation

The import map says, “When you write three, use this actual file.”

### Intermediate explanation

Modules let the browser load code in a structured and predictable way. Import maps make the source easier to maintain.

### Advanced explanation

ES modules provide static dependency resolution, which improves tooling, code organization, and tree-shaking opportunities.

## 1.7 Renderer, scene, and camera

### What they are

The renderer is the object that draws the scene to the canvas. The scene is the container for all objects. The camera defines the viewpoint from which the scene is observed.

### Why they exist

Without these three concepts, there would be no way to describe what should be visible and how it should be drawn. The renderer handles the draw call. The scene organizes the content. The camera provides the view.

### How they work internally

The renderer uses the camera and the scene together. It iterates through the objects in the scene, transforms them relative to the camera, and sends the result to the GPU.

### When to use them

You use them in almost every Three.js app. They are the foundation of the engine.

### Common mistakes

Beginners often forget to add the renderer’s canvas to the page or to update the camera aspect ratio on resize.

### Performance considerations

Creating many renderers or repeatedly resizing them can cost time. Keep the renderer configuration simple until you know you need more.

### Real-world uses

Every 3D scene relies on these concepts. They are the core of gaming, visualization, and simulation.

### Step-by-step implementation

In the code, we:

1. create a WebGLRenderer
2. set its size to the viewport dimensions
3. append the canvas to the document
4. create a Scene
5. create a PerspectiveCamera
6. set the camera position

### Beginner explanation

The renderer draws the scene, the scene holds the objects, and the camera looks at the scene.

### Intermediate explanation

The camera determines projection, clipping, and field of view. The renderer turns the scene from that viewpoint into pixels.

### Advanced explanation

The renderer and camera work together to define a view frustum, which is the visible region of 3D space that can be projected onto the screen.

## 1.8 Geometry, materials, and meshes

### What they are

A geometry describes the shape of an object. A material describes how that shape looks. A mesh combines the two into a visible object that can be added to the scene.

### Why they exist

These three pieces separate shape from appearance from instantiation. This makes the system flexible and reusable.

### How they work internally

The geometry provides vertex positions and other data. The material provides the shader instructions and surface properties. The mesh holds a reference to both and participates in the scene graph.

### When to use them

You use them every time you create a visible object in Three.js.

### Common mistakes

A beginner might create geometry and material but forget to create a mesh. Another common issue is using a material that does not support the lighting or shading you expect.

### Performance considerations

Geometry and material instances can be shared. For example, many cubes can use the same geometry and a shared material, though they will still need their own mesh instances.

### Real-world uses

This pattern is used for everything from simple boxes to complex vehicles and characters.

### Step-by-step implementation

In our code, we create:

- BoxGeometry for a cube's shape
- MeshBasicMaterial for a simple solid color
- Mesh to combine them

### Beginner explanation

The shape is the geometry, the looks are the material, and the visible object is the mesh.

### Intermediate explanation

The mesh is the object that can be positioned, rotated, and scaled in the scene.

### Advanced explanation

The mesh is an Object3D that participates in the scene graph, and its transform can be inherited by children.

## 1.9 Creating a cube

### What it is

A cube is one of the simplest 3D shapes. In Three.js, it is created with BoxGeometry.

### Why it exists

It is a perfect first object because it is easy to understand and visually obvious.

### How it works internally

BoxGeometry builds a set of vertices that define six rectangular faces. Those vertices are connected into triangles, and the GPU draws those triangles.

### When to use it

Use cubes for prototypes, levels, boxes, blocks, and simple test objects.

### Common mistakes

A common beginner mistake is to forget that the cube’s size is given in units and not pixels. Another is to expect the cube to appear centered unless the camera is positioned properly.

### Performance considerations

A cube is trivial to render. It is an excellent first object because it teaches the basic pipeline without imposing heavy cost.

### Real-world uses

Cubes appear in games, UI experiments, data visualizations, and many 3D scene tests.

### Step-by-step implementation

In our code, the line:

```javascript
const geometry = new THREE.BoxGeometry(1, 1, 1);
```

creates a cube with a side length of 1 unit. Then we set a material and a mesh, and add that mesh to the scene.

### Beginner explanation

The cube is just a simple box made from geometry and color.

### Intermediate explanation

The cube becomes a mesh once it is paired with a material and added to the scene.

### Advanced explanation

The cube’s geometry is built from triangles that form the surface. The renderer handles those triangles as primitives.

## 1.10 The animation loop

### What it is

The animation loop is the repeated function that updates the scene and renders it every frame.

### Why it exists

Without the loop, the scene would appear static. The loop is what makes motion and changes possible.

### How it works internally

The browser calls the function you pass to requestAnimationFrame before the next repaint. In that function, we can update object transforms, process input, and render the scene.

### When to use it

Use it for animations, movement, and any continuously changing state.

### Common mistakes

A common mistake is to call renderer.render too often or to modify the scene from outside the loop. Another issue is to forget to use requestAnimationFrame rather than a time-based loop that runs too aggressively.

### Performance considerations

The animation loop should stay lightweight. Complex calculations should be minimized per frame.

### Real-world uses

It is used for gameplay logic, camera movement, particle effects, UI motion, and visual simulations.

### Step-by-step implementation

In our project, the animate function:

1. calls requestAnimationFrame(animate)
2. rotates the cube
3. renders the scene

### Beginner explanation

The animation loop keeps asking the browser to draw the scene again and again.

### Intermediate explanation

The loop is the heartbeat of the application. Each iteration updates state and then produces a frame.

### Advanced explanation

A smooth application needs to balance update work and render work so the frame budget is respected.

## 1.11 What happens each frame

### What it is

Each frame is one iteration of the render loop. During that frame, the application updates the scene and then renders it.

### Why it exists

The display is refreshed multiple times a second, and your app needs to respond to that timing.

### How it works internally

The browser schedules a repaint. When it is time to paint, your animate function runs. It mutates objects, updates any input state, and calls renderer.render(scene, camera). The renderer then prepares and submits draw commands to the GPU.

### When to use it

Every interactive 3D experience should have a frame loop.

### Common mistakes

A beginner might think that changing a property is enough. In reality, that change is only visible after the scene is rendered.

### Performance considerations

Each frame should do only the work necessary to achieve the desired result. Avoid expensive operations on each pass unless you really need them.

### Real-world uses

This model is how games, simulations, and interactive visual apps work.

### Step-by-step implementation

Our loop does the following:

- update the cube’s rotation
- call render

That is enough for a simple first scene.

### Beginner explanation

The app redraws the world again and again so motion appears.

### Intermediate explanation

The scene graph is evaluated as part of each frame and transformed into pixels.

### Advanced explanation

The frame loop is the bridge between application state and the browser’s paint cycle.

## 1.12 The full code file explained line by line

The file [course/project.html](course/project.html) is intentionally simple. Every section exists to teach one concept at a time.

### The HTML shell

The root html tag opens the document. The head contains metadata and styling. The body contains the page content and the script tags. This structure is standard for web pages.

### The style block

The CSS ensures that the page uses the entire viewport. It removes default body margin and prevents scrolling from interfering with the canvas. This matters because a 3D scene normally wants as much screen space as possible.

### The import map

The import map gives Three.js a friendly module name. Instead of writing a long URL every time, we can write import * as THREE from 'three'.

### The module script

The script is marked as a module because it uses the import keyword. That is how modern browsers understand Three.js imports.

### A few important objects

- renderer: draws the scene to canvas
- scene: stores the 3D world
- camera: defines the viewpoint
- geometry: defines the cube’s shape
- material: defines the visible surface
- mesh: combines shape and appearance into an object

### The render call

The renderer.render(scene, camera) line is the point where the scene is turned into pixels. It is the result of all the earlier setup.

### The resize handler

The resize handler keeps the renderer, canvas, and camera in sync after the browser window changes size.

## 1.13 Common beginner mistakes

### Mistake 1: forgetting to append the renderer canvas

If you create a renderer but never add its canvas to the page, the scene will not appear.

### Mistake 2: forgetting to add the mesh to the scene

A mesh is only visible if it is part of the scene.

### Mistake 3: using the wrong camera type

PerspectiveCamera is the default choice for realistic 3D vision. If you use the wrong projection, your scene may look distorted or appear unexpectedly.

### Mistake 4: forgetting to update the camera aspect ratio

On resize, the projection matrix must be updated. Otherwise the scene may stretch.

### Mistake 5: expecting the object to render without an animation loop

The scene will not update unless you render it repeatedly.

## 1.14 Mini project: make the cube bounce slowly

Try modifying the scene so that the cube moves back and forth while still rotating. This introduces the idea that transforms change over time.

### Suggested steps

1. Create a variable such as let zOffset = 0;.
2. In the animation loop, update the cube position using a sine wave.
3. Render the scene again.

### Example idea

```javascript
let zOffset = 0;

function animate() {
  requestAnimationFrame(animate);
  zOffset += 0.02;
  cube.position.z = Math.sin(zOffset) * 2;
  cube.rotation.x += 0.01;
  cube.rotation.y += 0.01;
  renderer.render(scene, camera);
}
```

### Why this matters

It teaches you that objects can be animated by changing their transform values over time.

## 1.15 Challenge problem

Create a scene with two cubes instead of one. Make one cube rotate faster than the other. Add a small color difference so they are easy to distinguish.

### What you should learn

- how multiple meshes coexist in one scene
- how to manage more than one object
- how to use the same render loop for multiple animated objects

## 1.16 Troubleshooting

### Problem: The canvas is blank

Possible causes:

- the renderer was never appended to the DOM
- the scene is empty
- the camera points away from the object
- the object is too far from the camera

### Problem: The cube is stretched

Possible causes:

- the camera aspect ratio was not updated on resize
- the renderer size is wrong

### Problem: The scene does not animate

Possible causes:

- requestAnimationFrame was never called
- the animation loop stopped
- the render call is missing

### Problem: The browser throws a module import error

Possible causes:

- the import map is missing or malformed
- the script type is not module
- the CDN URL is not accessible

## 1.17 Summary

In this chapter, you learned the foundations of Three.js:

- What Three.js is and why it exists
- How WebGL sits underneath it
- How the browser rendering pipeline works
- How an HTML file can host a 3D scene
- How JavaScript modules and import maps work
- How the renderer, scene, and camera fit together
- How geometry, material, and mesh combine to form visible objects
- How to create a cube
- How the animation loop works
- How the scene is rendered each frame

You also built the first version of the course project: a simple rotating cube inside a browser-based 3D scene. That is a strong first step, and it establishes the vocabulary we will use in all future chapters.

The next chapter will continue expanding the same project and introduce transformations in more depth. As soon as you want to continue, reply with the word next and I will continue from this exact point without repeating earlier material.
