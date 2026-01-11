# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Minecraft-like 3D first-person block-building game implemented as a single-page HTML/JavaScript application using Three.js. Players can explore procedurally generated terrain, place and destroy blocks, and customize game settings.

## Running the Game

Open `fps_game_unified.html` directly in a browser (latest version). No build step or server required.

### Version History
- `fps_game-7.html` - Working baseline with individual block meshes (slow on large worlds)
- `fps_game-10.html` - Attempted chunk optimization (has rendering bugs)
- `fps_game-11.html` - Added exposed-face culling to v7
- `fps_game-12.html` - Merged geometry chunks + render distance + dynamic chunk loading
- `fps_game-13.html` - Custom textures per block face via PNG upload
- `fps_game_day_night.html` - Day-night cycle with sun, moon, stars, and seasons
- `fps_game_unified.html` - **Current**: Merged textures + day-night cycle features

## Architecture

### Single-File Structure
Each HTML file is self-contained with embedded CSS and JavaScript. Three.js loaded from CDN (r128).

### Core Systems (fps_game_unified.html)

- **Chunk System**: World divided into 16×16×FULL_HEIGHT chunks with merged geometry per chunk. Key functions:
  - `createChunkMesh(cx, cz)` - Generates merged BufferGeometry with vertex colors
  - `rebuildChunk(cx, cz)` - Rebuilds a chunk's mesh
  - `updateVisibleChunks(playerX, playerZ)` - Loads/unloads chunks based on render distance

- **Voxel Storage**:
  - `heightMap[x][z]` - Procedural terrain heights
  - `voxelData` object - Player modifications (key: "x,y,z", value: blockType or 0 for destroyed)
  - `getVoxel(x,y,z)` - Checks voxelData first, falls back to procedural
  - `setVoxel(x,y,z,type)` - Stores modification and rebuilds affected chunks

- **Render Distance**: Configurable 1-12 chunks via menu slider
  - Controls which chunks are loaded/rendered
  - Fog fades terrain (starts at 60%, full at 100% of render distance)
  - Shadow frustum matches render distance

- **Face Culling**: Only exposed faces (with empty neighbors) are included in chunk mesh. Checked via `getVoxel()` for each of 6 directions.

- **Texture System**: Custom PNG textures can be uploaded per block type per face
  - UI: Collapsible "Block Textures" section in menu with matrix grid
  - Storage: `blockTextureData[blockType][faceIndex]` stores base64 data URLs
  - Persistence: Textures saved to localStorage, restored on page load
  - Rendering: When a block type has textures, uses geometry groups with material arrays
  - Face indices: 0=Top, 1=Bottom, 2=Right, 3=Left, 4=Front, 5=Back

- **Day-Night Cycle**: Full day-night simulation with seasonal variations
  - `timeOfDay` - 0=midnight, 0.25=sunrise, 0.5=noon, 0.75=sunset
  - `DAY_CYCLE_DURATION = 1200` seconds (20 minutes per full day)
  - `updateDayNightCycle(delta)` - Updates sun/moon positions, lighting, sky colors
  - Sun/Moon spheres orbit around player with seasonal arc height variations
  - Star field visible at night with automatic fade in/out at twilight
  - Optional sun/moon glare effects (additive blending sprites)
  - Seasons: Spring, Summer, Autumn, Winter (5 days each)
  - Season affects max sun angle (0.4-0.9) and day length multiplier

- **Coordinate System**: Block at voxel (x, y, z) spans:
  - X: x-0.5 to x+0.5
  - Y: y-1 to y (bottom to top)
  - Z: z-0.5 to z+0.5

### Key Constants
- `CHUNK_SIZE = 16` - Voxels per chunk (horizontal)
- `BLOCK_SIZE = 1` - World units per block
- `REACH_DISTANCE = 10` - Block interaction range
- `RENDER_DISTANCE` - Chunks visible around player (1-12, default 6)

### Block Types
Terrain: 1=Grass, 2=Dirt, 3=Stone
Player blocks: 4-11 (mapped from `blockTypes` array with colors)

### Known Issues
- Shadow bleeding at block edges (mitigated with normalBias=0.15)
- Reflection artifacts may appear at block intersections when reflections enabled
