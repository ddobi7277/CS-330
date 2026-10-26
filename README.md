# CS-330


This repository holds my final project for CS-330. The project recreates a 2D photograph of a desk workspace as a fully realized 3D scene in OpenGL, built entirely from primitive shapes.

## Contents

- `3DScene.zip` — the Visual Studio project, all source files, and the compiled executable
- `Design_Decisions.docx` — the written explanation of my development choices

## About the Scene

The scene recreates a desk workspace: a widescreen monitor with an aluminum stand, a full keyboard, a mouse, a stack of five notebooks, a dark ceramic coffee mug, and a square cup holding six sharpened pencils, all on a varnished wood desk against a drywall wall.

Everything is assembled from primitives — planes, boxes, cylinders, cones, spheres, a tapered cylinder, and a torus. The scene is lit by two point lights using the full Phong model, textured with wood, drywall, and ceramic images, and navigable with WASD, QE, mouse look, and a key toggle between perspective and orthographic projection.

---

## Reflection

### How do I approach designing software?

The design skill this project forced on me was decomposition into primitives. When you look at a photograph of a desk, you see a monitor and a mug. You do not see five boxes and a flipped tapered cylinder with a torus attached. Learning to look at a real object and ask what the smallest set of basic shapes is that still reads as that object changed how I start a problem. The mug is the clearest example: a tapered cylinder turned upside down so its wide end becomes the rim, with the top cap left off so it reads as open rather than solid, a thin cylinder standing in for the coffee surface, and a torus for the handle.

My process was to start from the reference photograph and work outward from the largest shapes to the smallest. I placed the desk and the wall first, because everything else sits relative to them, then the monitor, and only then the small objects. Within each object I did the same thing: the mug body before the handle, the pencil cup before the pencils.

The tactic I expect to reuse most is deriving positions from constants instead of hardcoding them. The mug handle is positioned from the mug's own radius and height, so resizing the mug moves the handle automatically. Early on I typed in literal coordinates, and every adjustment broke something downstream. Naming the dimensions once and computing everything else from them is the same discipline that keeps any codebase from turning brittle.

### How do I approach developing programs?

The development strategy that made the biggest difference was writing parameterized helper functions instead of repeating draw calls. `DrawPencil` takes a base position and two tilt angles and builds a whole pencil from a cylinder and two cones, computing the direction vector so the cones land correctly on a leaning pencil. Six calls produce the full cup. `DrawNotebook` takes a position, a size, a turn angle, and a cover color, and assembles the four boxes of one notebook. Five calls produce the stack. Together those two functions replaced roughly thirty repetitive blocks.

Iteration was the entire shape of this project. Each milestone added one layer to the same scene: shapes, then camera navigation, then textures, then lighting. Because the scene was never rebuilt from scratch, every weakness I left in an early milestone came back. Objects I had positioned by eye looked fine flat-shaded and looked wrong once lighting revealed their actual geometry.

My approach evolved from writing everything in one long `RenderScene` to splitting it into grouping functions, one per object, each owning its own constants. That meant adjusting the monitor could not accidentally break the mug. I also got much better at debugging by reasoning about the math rather than guessing. The clearest case was transformation order: the model matrix multiplies translation by rotation by scale, so a mesh always rotates around its own origin before it is moved. When I tilted the notebooks a few degrees for a less tidy stack, the covers turned but the spine stayed pointing in its original direction, because I was passing unrotated offsets as positions. Once I understood the rule, several problems I had been treating as separate bugs turned out to be the same one.

### How can computer science help me in reaching my goals?

Educationally, this course gave me a concrete grounding in linear algebra. I had seen matrices in a math class, but building a scene where a wrong multiplication order visibly breaks the image made transformations real in a way an exam never did. That carries directly into any course touching simulation, machine learning, or game development, where the same matrix operations show up under different names.

Professionally, the transferable skill is turning a visual requirement into working code. A client hands over a reference image and expects a recognizable result within real constraints, in this case a polygon budget low enough to 3D print. That is the same negotiation between fidelity and cost that shows up in front-end work, data visualization, and simulation. The specific API also transfers: the shader concepts and matrix math behind this project are the same ones behind WebGL and Three.js, which would let me build interactive 3D content that runs in a browser rather than as a desktop application.
