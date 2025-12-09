# [![Bevy](https://bevy.org/assets/bevy_logo_light_dark_and_dimmed.svg)](https://bevy.org)

[![License](https://img.shields.io/badge/license-MIT%2FApache-blue.svg)](https://github.com/bevyengine/bevy#license)
[![Crates.io](https://img.shields.io/crates/v/bevy.svg)](https://crates.io/crates/bevy)
[![Downloads](https://img.shields.io/crates/d/bevy.svg)](https://crates.io/crates/bevy)
[![Docs](https://docs.rs/bevy/badge.svg)](https://docs.rs/bevy/latest/bevy/)
[![CI](https://github.com/bevyengine/bevy/workflows/CI/badge.svg)](https://github.com/bevyengine/bevy/actions)
[![Discord](https://img.shields.io/discord/691052431525675048.svg?label=&logo=discord&logoColor=ffffff&color=7389D8&labelColor=6A7EC2)](https://discord.gg/bevy)

---

## TerraFirma Fork - Earthworks & Voxel Terrain

**This is a fork of Bevy adding earthworks/terrain simulation capabilities via the `bevy_earthworks` crate.**

### Quick Start

```bash
# Run the interactive demo with terrain and construction machines
cargo run -p bevy_earthworks --example interactive

# Run the minimal example
cargo run -p bevy_earthworks --example minimal
```

### What's Added: `bevy_earthworks` Crate

A complete volumetric voxel terrain and construction machine simulation plugin for building earthworks visualization and games.

#### Voxel Terrain System
- **16³ chunk-based terrain** with efficient sparse storage
- **8 material types**: Air, Dirt, Clay, Rock, Topsoil, Gravel, Sand, Water
- **Greedy meshing** algorithm for optimized rendering
- **Terrain texture atlas** for PBR material support
- **Async chunk meshing** with task pool for performance
- **LOD system** based on camera distance
- **Terrain operations**: excavate spheres, fill boxes, modify individual voxels

#### Construction Machine Simulation
- **4 machine types**: Excavator, Bulldozer, Wheel Loader, Dump Truck
- **Work envelopes**: Toroidal (excavators), Rectangular (dozers), Arc (loaders)
- **Direct control**: WASD movement, blade up/down, rotation
- **Terrain interaction**: collision detection, terrain following, material pushing
- **GLTF model pipeline** with procedural geometry fallbacks

#### Plan Execution System
- **JSON execution plans** for pre-computed machine operations
- **Actions**: MoveTo, Excavate, Dump, Push, Idle, WaitFor
- **Timeline playback** with play/pause, seek, speed control (0.5x-4x)
- **Machine coordination** and dependency handling

#### RTS-Style UI
- **Minimap** with machine positions and camera viewport indicator
- **Selection panel** with unit portrait, health bar, status
- **Command card** (3x3 grid) with hotkey hints
- **Timeline UI** with playback controls
- **On-screen HUD**: blade status, load bar, position

#### Visual Effects
- **HDR rendering** with TonyMcMapface tonemapping
- **Bloom post-processing** for natural highlights
- **Distance fog** with atmospheric haze
- **Shadow cascades** (4 cascades, 150m range)
- **Camera shake** with trauma-based decay
- **Orbit camera** with smooth controls

#### Game Systems
- **Scoring system** with progress tracking
- **Achievement system** (Zyns) with unlock events
- **AI agent system** for automated testing
- **Testing utilities** module

### Controls (Interactive Example)

| Input | Action |
|-------|--------|
| **Camera** | |
| Right mouse drag | Orbit camera |
| Middle mouse drag | Pan camera |
| Scroll wheel | Zoom in/out |
| W/A/S/D | Move camera target |
| Q/E | Move camera up/down |
| Shift | Move faster |
| **Playback** | |
| Space | Toggle play/pause |
| R | Reset to start |
| 1/2/3/4 | Speed 0.5x/1x/2x/4x |
| **Direct Control** | |
| Arrow keys | Move machine |
| Z/X | Blade up/down |

### Architecture

```
crates/bevy_earthworks/
├── terrain/       # Voxel terrain system
│   ├── voxel.rs      # Voxel and VoxelTerrain
│   ├── chunk.rs      # 16³ chunk storage
│   ├── meshing.rs    # Simple & greedy meshing
│   ├── materials.rs  # Material definitions
│   ├── textures.rs   # Terrain texture atlas
│   └── operations.rs # Terrain modification
├── machines/      # Construction machines
│   ├── components.rs    # Machine, WorkEnvelope, Mobility
│   ├── direct_control.rs # Player input handling
│   ├── animation.rs     # Movement animation
│   └── gizmos.rs        # Work envelope visualization
├── models/        # GLTF model pipeline
│   ├── registry.rs   # Model loading registry
│   ├── procedural.rs # Procedural geometry fallbacks
│   └── spawner.rs    # Unified machine spawning
├── plan/          # Execution plans
│   ├── schema.rs     # Plan data structures
│   ├── loader.rs     # JSON asset loader
│   ├── playback.rs   # Playback control
│   └── executor.rs   # Step execution
├── ui/            # User interface
│   ├── mod.rs        # Timeline UI
│   ├── minimap.rs    # RTS minimap
│   └── selection.rs  # Selection panel & commands
├── camera/        # Orbit camera with shake
├── scoring/       # Progress tracking
├── zyns/          # Achievement system
├── testing/       # AI agents & test utilities
├── effects/       # Visual effects
└── jobs/          # Job queue system
```

### Usage Example

```rust
use bevy::prelude::*;
use bevy_earthworks::prelude::*;

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .add_plugins(EarthworksPlugin::default())
        .add_systems(Startup, setup)
        .run();
}

fn setup(mut commands: Commands, mut terrain: ResMut<VoxelTerrain>) {
    // Spawn camera with orbit controls
    commands.spawn((
        Camera3d::default(),
        Transform::from_xyz(30.0, 20.0, 30.0).looking_at(Vec3::ZERO, Vec3::Y),
        OrbitCamera::new().with_target(Vec3::ZERO).with_distance(40.0),
    ));

    // Create terrain chunk
    let mut chunk = Chunk::new();
    for x in 0..16 {
        for z in 0..16 {
            for y in 0..4 {
                chunk.set(x, y, z, Voxel::solid(MaterialId::Dirt));
            }
        }
    }
    // ... spawn chunk entity
}
```

See [`crates/bevy_earthworks/WALKTHROUGH.md`](crates/bevy_earthworks/WALKTHROUGH.md) for the complete guide.

---

## What is Bevy?

Bevy is a refreshingly simple data-driven game engine built in Rust. It is free and open-source forever!

## WARNING

Bevy is still in the early stages of development. Important features are missing. Documentation is sparse. A new version of Bevy containing breaking changes to the API is released [approximately once every 3 months](https://bevy.org/news/bevy-0-6/#the-train-release-schedule). We provide [migration guides](https://bevy.org/learn/migration-guides/), but we can't guarantee migrations will always be easy. Use only if you are willing to work in this environment.

**MSRV:** Bevy relies heavily on improvements in the Rust language and compiler.
As a result, the Minimum Supported Rust Version (MSRV) is generally close to "the latest stable release" of Rust.

## Design Goals

* **Capable**: Offer a complete 2D and 3D feature set
* **Simple**: Easy for newbies to pick up, but infinitely flexible for power users
* **Data Focused**: Data-oriented architecture using the Entity Component System paradigm
* **Modular**: Use only what you need. Replace what you don't like
* **Fast**: App logic should run quickly, and when possible, in parallel
* **Productive**: Changes should compile quickly ... waiting isn't fun

## About

* **[Features](https://bevy.org):** A quick overview of Bevy's features.
* **[News](https://bevy.org/news/)**: A development blog that covers our progress, plans and shiny new features.

## Docs

* **[Quick Start Guide](https://bevy.org/learn/quick-start/introduction):** Bevy's official Quick Start Guide. The best place to start learning Bevy.
* **[Bevy Rust API Docs](https://docs.rs/bevy):** Bevy's Rust API docs, which are automatically generated from the doc comments in this repo.
* **[Official Examples](https://github.com/bevyengine/bevy/tree/latest/examples):** Bevy's dedicated, runnable examples, which are great for digging into specific concepts.
* **[Community-Made Learning Resources](https://bevy.org/assets/#learning)**: More tutorials, documentation, and examples made by the Bevy community.

## Community

Before contributing or participating in discussions with the community, you should familiarize yourself with our [**Code of Conduct**](./CODE_OF_CONDUCT.md).

* **[Discord](https://discord.gg/bevy):** Bevy's official discord server.
* **[Reddit](https://reddit.com/r/bevy):** Bevy's official subreddit.
* **[GitHub Discussions](https://github.com/bevyengine/bevy/discussions):** The best place for questions about Bevy, answered right here!
* **[Bevy Assets](https://bevy.org/assets/):** A collection of awesome Bevy projects, tools, plugins and learning materials.

### Contributing

If you'd like to help build Bevy, check out the **[Contributor's Guide](https://bevy.org/learn/contribute/introduction)**.
For simple problems, feel free to [open an issue](https://github.com/bevyengine/bevy/issues) or
[PR](https://github.com/bevyengine/bevy/pulls) and tackle it yourself!

For more complex architecture decisions and experimental mad science, please open an [RFC](https://github.com/bevyengine/rfcs) (Request For Comments) so we can brainstorm together effectively!

## Getting Started

We recommend checking out the [Quick Start Guide](https://bevy.org/learn/quick-start/introduction) for a brief introduction.

Follow the [Setup guide](https://bevy.org/learn/quick-start/getting-started/setup) to ensure your development environment is set up correctly.
Once set up, you can quickly try out the [examples](https://github.com/bevyengine/bevy/tree/latest/examples) by cloning this repo and running the following commands:

```sh
# Switch to the correct version (latest release, default is main development branch)
git checkout latest
# Runs the "breakout" example
cargo run --example breakout
```

To draw a window with standard functionality enabled, use:

```rust
use bevy::prelude::*;

fn main() {
    App::new()
        .add_plugins(DefaultPlugins)
        .run();
}
```

### Fast Compiles

Bevy can be built just fine using default configuration on stable Rust. However for really fast iterative compiles, you should enable the "fast compiles" setup by [following the instructions here](https://bevy.org/learn/quick-start/getting-started/setup).

## [Bevy Cargo Features][cargo_features]

This [list][cargo_features] outlines the different cargo features supported by Bevy. These allow you to customize the Bevy feature set for your use-case.

[cargo_features]: docs/cargo_features.md

## Thanks

Bevy is the result of the hard work of many people. A huge thanks to all Bevy contributors, the many open source projects that have come before us, the [Rust gamedev ecosystem](https://arewegameyet.rs/), and the many libraries we build on.

A huge thanks to Bevy's [generous sponsors](https://bevy.org). Bevy will always be free and open source, but it isn't free to make. Please consider [sponsoring our work](https://bevy.org/donate/) if you like what we're building.

<!-- This next line need to stay exactly as is. It is required for BrowserStack sponsorship. -->
This project is tested with BrowserStack.

## License

Bevy is free, open source and permissively licensed!
Except where noted (below and/or in individual files), all code in this repository is dual-licensed under either:

* MIT License ([LICENSE-MIT](LICENSE-MIT) or [http://opensource.org/licenses/MIT](http://opensource.org/licenses/MIT))
* Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or [http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0))

at your option.
This means you can select the license you prefer!
This dual-licensing approach is the de-facto standard in the Rust ecosystem and there are [very good reasons](https://github.com/bevyengine/bevy/issues/2373) to include both.

Some of the engine's code carries additional copyright notices and license terms due to their external origins.
These are generally BSD-like, but exact details vary by crate:
If the README of a crate contains a 'License' header (or similar), the additional copyright notices and license terms applicable to that crate will be listed.
The above licensing requirement still applies to contributions to those crates, and sections of those crates will carry those license terms.
The [license](https://doc.rust-lang.org/cargo/reference/manifest.html#the-license-and-license-file-fields) field of each crate will also reflect this.

The [assets](assets) included in this repository (for our [examples](./examples/README.md)) typically fall under different open licenses.
These will not be included in your game (unless copied in by you), and they are not distributed in the published bevy crates.
See [CREDITS.md](CREDITS.md) for the details of the licenses of those files.

### Your contributions

Unless you explicitly state otherwise,
any contribution intentionally submitted for inclusion in the work by you,
as defined in the Apache-2.0 license,
shall be dual licensed as above,
without any additional terms or conditions.
