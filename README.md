# Tomb Saviour

A first-person 3D action game written in C++ with modern OpenGL and NVIDIA PhysX. You play a mummy defending its pyramid in the desert. Collect cactus spikes, throw them at the intruders and survive until every enemy is down.

Built as a team project for the Computer Graphics lab course (UE) at TU Wien, summer semester 2023.

## Gameplay

You start next to a pyramid in a sandy, hilly desert with palm trees, cacti and a video wall. An enemy chases you across the terrain and attacks as soon as it reaches you.

- **Collect** Walk up to a cactus and pick it up. Each cactus gives you 5 spikes.
- **Fight** Throw spikes in the direction you are looking. A hit plays the enemy's death animation and removes it from the scene.
- **Survive** Staying in close contact with an enemy costs you lives. Lives are shown as scarabs in the HUD and turn grey when lost.
- **Win or lose** Defeat all enemies to see the win screen. Lose all lives and the lose screen appears.

## Controls

| Key | Action |
|---|---|
| `W` `A` `S` `D` | Move |
| Mouse | Look around |
| `Space` (hold) | Sprint |
| `9` | Pick up nearby cactus |
| `0` | Throw spike |
| `B` | Reset to start position |
| `C` | Switch camera mode (mouse look / edge steering) |
| `N` | Toggle normal mapping |
| `F1` | Toggle wireframe mode |
| `F2` | Toggle backface culling |
| `F5` | Toggle fullscreen |
| `Esc` | Quit |

## Technical highlights

**Rendering (OpenGL 4.5 core)**
- Terrain generated from a heightmap with tessellation shaders and distance-based level of detail (tessellation level 4 to 64)
- Phong lighting with support for point, directional and spot lights
- Normal mapping with per-triangle tangent and bitangent calculation, switchable at runtime
- Video texture on an in-world screen, decoded frame by frame with OpenCV
- Compressed DDS textures and stb_image for PNG/JPG

**Animation**
- Skeletal animation for enemies loaded with Assimp from Collada (`.dae`) files
- Up to 4 bone weights per vertex and 200 bones per model, skinning on the GPU
- Keyframe interpolation for position, rotation and scale
- Separate idle, punching and dying animations depending on game state

**Physics (NVIDIA PhysX)**
- Capsule character controllers for the player and the enemies
- PhysX height field built from the same heightmap as the rendered terrain
- Cooked triangle meshes as static colliders for the pyramid, palm tree and video wall
- Spikes as dynamic rigid bodies thrown with an impulse, hits detected through contact callbacks

**HUD**
- Orthographic overlay for spike counter, lives and fullscreen win/lose screens
- Layout recalculated when the window is resized

## Tech stack

C++17, OpenGL 4.5, GLSL, GLFW, GLEW, GLM, NVIDIA PhysX, Assimp, OpenCV, stb_image, inih, CMake

## Project structure

```
├── ECG_Solution/
│   ├── src/
│   │   ├── Main.cpp           window setup, render loop
│   │   ├── Objects.*          creates and renders all scene objects, win/lose check
│   │   ├── PhysxScene.*       PhysX setup, colliders, picking up and throwing spikes
│   │   ├── Character.*        player controller and WASD movement
│   │   ├── Enemy.*            enemy AI (follows the player), animations, damage
│   │   ├── Camera.*           first-person camera, projection matrices
│   │   ├── Terrain.*          tessellated heightmap terrain
│   │   ├── Shader.*           shader programs, textures, lights, video texture
│   │   ├── Hdu.*              HUD overlay
│   │   ├── Drawables/         Assimp model loader with skeletal animation
│   │   ├── Lights/            point, directional and spot light classes
│   │   └── Utils/             input callbacks, transforms, INI reader, globals
│   ├── cmake/                 build settings and source lists
│   └── CMakeLists.txt
├── assets/
│   ├── shaders/               vertex, fragment and tessellation shaders
│   ├── heightmaps/            terrain heightmaps
│   ├── models/                animated characters (.dae)
│   ├── objects/               static meshes (.obj)
│   ├── textures/              surface textures and HUD images
│   ├── videos/                video wall footage
│   └── settings.ini           window, camera and gameplay settings
├── external/                  third-party headers and prebuilt libraries
└── install_dependencies.sh    dependency setup for Ubuntu
```

## Building and running

The project is set up primarily for **Windows with Visual Studio 2022** (MSVC v143). The prebuilt PhysX, Assimp and OpenCV libraries and DLLs are included in the repository and copied next to the executable during the build.

1. Clone the repository
   ```bash
   git clone https://github.com/melli27/cgue23-TombSaviour.git
   ```
2. Open the `ECG_Solution` folder in Visual Studio as a CMake project
3. Select the `debug` or `release` preset and build `ECG_Solution`
4. Run the executable with the **repository root as working directory**, since assets are loaded from relative paths such as `assets/settings.ini`

CMake presets for Linux (`linux-debug`, `linux-release`) and an Ubuntu setup script (`install_dependencies.sh`) come from the course framework. The game code itself targets Windows, as the bundled libraries are Windows builds.

## Configuration

Settings are read from `assets/settings.ini` at startup.

```ini
[window]
width = 800
height = 800
refresh_rate = 60

[camera]
fov = 60.0
near = 0.1
far = 1000.0

[global]
maxlifes = 4
```
