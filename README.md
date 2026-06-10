# Fable-DOOM — A Raycaster DOOM Clone

A DOOM-style shooter built as a **single HTML file** with no dependencies, no assets, and no build step. Every texture, sprite, and sound effect is generated procedurally in code. Open `index.html` in a browser and play.

The real purpose of this project is **educational**: the raycasting engine is heavily commented (in Spanish) and structured as a guided reading, so you can learn how Wolfenstein 3D / DOOM-era pseudo-3D rendering actually works.

## How it was made

This entire game was generated in **one shot** (a single prompt, single response) by **Claude Fable 5**, Anthropic's frontier model, running inside Claude Code.

### The prompt

The original prompt (in Spanish):

> *"Quiero crear un juego shooter clon de doom con implementacion de raycaster. Mi proposito es aprender como funciona el ray casting. Crea este juego en html y haz el codigo del ray caster lo mas entendible posible con muchos comentarios en español"*

Translation:

> *"I want to create a DOOM clone shooter with a raycaster implementation. My goal is to learn how ray casting works. Build this game in HTML and make the raycaster code as understandable as possible, with lots of comments in Spanish."*

### The model

**Claude Fable 5** (`claude-fable-5`) is the first model in Anthropic's Claude 5 family and part of the Mythos-class tier, which sits above Claude Opus in capability. More info: https://www.anthropic.com/news/claude-fable-5-mythos-5

From that single prompt, the model produced in one pass:

- A complete DDA raycasting engine with textured walls, distance fog, and side shading
- Sprite projection with camera-space transformation and z-buffer occlusion
- 10 enemies with a simple AI (sleep → spot/hear player → chase → bite)
- Hitscan shooting with muzzle flash, recoil, and weapon bobbing
- Procedural 64×64 textures and sprites (brick, stone, tech panel, demon, pickups)
- Synthesized sound effects via WebAudio (no audio files)
- Minimap, DOOM-style HUD, health/ammo pickups, win/death states
- A 24×24 level designed in ASCII, which the model then **validated programmatically** with a flood-fill script to confirm every walkable cell and the exit were reachable before declaring the game done

## How to play

| Input | Action |
|---|---|
| `W A S D` | Move / strafe |
| Mouse (click to capture) or `← →` | Turn |
| Click / `Space` | Shoot |

Kill the demons and reach the green **EXIT** panel.

## Learning the raycaster

The code in `index.html` is meant to be read in order. Key stops:

1. **What is raycasting?** (top of the script) — the "one ray per screen column" idea, with ASCII diagrams.
2. **The camera** — direction vector + camera plane, and why `ray = dir + plane · cameraX` produces the field of view.
3. **`lanzarRayo()`** — the DDA algorithm, the heart of the engine: jumping from grid boundary to grid boundary instead of marching in tiny steps.
4. **Fisheye correction** — why the *perpendicular* distance to the camera plane is used instead of the ray's true length.
5. **`dibujarMundo()`** — the entire "3D projection" is one division: `lineHeight = screenHeight / distance`.
6. **`dibujarSprites()`** — projecting enemies with the inverse camera matrix and occluding them with the z-buffer.

### Suggested experiments

- Change `planoY: 0.66` to `1.0` and watch the FOV widen.
- Replace the perpendicular distance with the Euclidean ray length and watch the walls curve (the fisheye effect).
- Edit `MAPA_TEXTO` to design your own level — `1`-`4` are wall textures, `E` enemies, `M`/`A` pickups, `S` the exit.
- Shrink the canvas `width` to see the individual rays as fat vertical strips.
