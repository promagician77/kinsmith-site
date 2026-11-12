# Kinsmith: modular character system demo

A playable model of a stylised low-poly fantasy character system: five races (human, high elf, wood elf, dwarf, orc) in two body types on one 28-bone skeleton, modular clothing and armour, palette textures, six shared animation clips and glTF export.

Live pages: `index.html` (the system) and `notes.html` (build notes).

## Measured

| Check | Result |
|---|---|
| Unit tests | 11 of 11 pass |
| One body topology | 1 vertex order across 60 bodies per detail level (5 races × 2 body types × 3 heights × 2 builds). LOD0 body 1,094 vertices, 2,084 triangles; LOD1 624 and 1,184 |
| Dressed triangle counts | LOD0 2,477–3,596 (median 3,077); LOD1 1,419–2,156 |
| Skin weights | 0 invalid across 240,834 vertices in 120 builds (at most 4 bones, weights sum to 1) |
| Clothing follows the body | 69,600 fitted clothing points carry exactly the weights of the body point beneath them: 0 mismatches |
| Hidden faces under clothing | Knight removes 2,184–2,224 triangles, villager 1,200–1,240, base mesh 64–104 |
| Animation clips | 6 clips, 26 tracks each, 0 tracks that fail to bind, every loop closes (largest seam 5e-17) |
| Same clips on every race | 150 of 156 exported tracks are the same objects for a human and a dwarf; only Root height differs (up to 4.87 cm) |
| Feet on the floor | Lowest point stays between -0.59 and +0.2 cm over 2,880 frames (30 bodies × 6 clips × 16 frames). Without grounding it reached -8.27 cm |
| Hands entering the body | 0 mm in all 2,880 frames |
| Clothing staying outside | 5 of 6 clips never put a fitted clothing point inside its body point. Run: deepest knee bend folds 66 point-frames up to 3.31 mm into the knee crease |
| glTF export | 6 files (4 races, both detail levels): 0 errors, 0 warnings, 0 infos in the Khronos glTF Validator. Each has 1 mesh, 28 joints, 1 material, 1 image, 6 animations, 304–393 KB |
| Export reloads and plays | Every file reloads in three.js with 28 joints and 6 clips; Walk moves the left foot 20.7–32.4 cm |
| Draw calls | 1 per character: 2, 11 and 36 including the floor disc, for 1, 10 and 35 characters |
| Build speed | Median 3.79 ms per character in Node; in the browser median 5.8 ms, slowest 22.3 ms over 60 race, body and outfit switches |
| Animation load | 35 animated characters: clips, grounding and bone matrices take 0.5 ms median on the CPU; whole-frame CPU 2.9 ms median |
| Recolouring | Repaints the 64×32 atlas; geometry object unchanged (same UUID) |
| Accessibility and layout | axe-core: 0 violations in 5 scans; arrow keys move between tabs; 5 of 5 viewport sizes without horizontal overflow |
| Download size | 742 KB of JavaScript before compression |

## Not measured

- Frames per second on real GPUs, phones or consoles (only software WebGL here)
- Import into Unity, Unreal or Godot: files pass the Khronos validator and reload in three.js, nothing more
- How closely it matches your reference sheets: this demo is generated in code; production meshes would be modelled to your references
- Facial rig, expression blend shapes, cloth or hair simulation
- Engine-side foot IK (grounding here is a root height offset)
- Long hair, capes and robe skirts against the legs during animation (checked by eye only)
- Normal maps, hand-painted detail or PBR textures beyond flat palette colours
- Lighthouse scores

## Run

```
npm ci
npm run dev                      # local viewer
npm test                         # unit tests
npx tsx tools/measure.ts         # writes out/measure.json
npm run build && npx vite preview --port 5180
node tools/qa.mjs http://localhost:5180   # needs a Chromium; set CHROME=/path/to/chrome
node tools/docs.mjs              # rebuilds notes, README and deploy/
```

## Deploy

```
cd deploy
npx vercel deploy --prod
```

`deploy/vercel.json` sends `X-Robots-Tag: noindex, nofollow` on every response and caches hashed assets for a year.

## Layout

- `src/char/params.ts`: races, sliders, outfit slots and presets, palette
- `src/char/rig.ts`: proportions and the shared skeleton
- `src/char/body.ts`, `hair.ts`, `gear.ts`: body, head and face, hair, clothing, armour, weapons
- `src/char/character.ts`: assembly into one skinned mesh and hidden-face removal
- `src/char/anim.ts`, `ground.ts`: clips and floor contact
- `src/char/measure.ts`, `tools/`: measurements and browser checks
