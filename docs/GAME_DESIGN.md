# PAPER.IO 2 WEB — TECHNICAL DESIGN DOCUMENT

## 1. ARCHITECTURE (Single-file, Zero-dependency)
```
┌─────────────────────────────────────────────┐
│ paperio2/index.html                         │
│ ├─ <style>   UI/HUD/menu styling            │
│ ├─ <canvas>  game layer                     │
│ ├─ HUD divs  score, leaderboard, deathscreen│
│ └─ <script>                                │
│    ├─ CONFIG (tuning constants)             │
│    ├─ Grid  (100×100 Uint8Array owner map)  │
│    ├─ Camera (lerp follow + dynamic zoom)   │
│    ├─ Player (state: pos, dir, trail, area) │
│    ├─ Bots[] (6 personality types)          │
│    ├─ Physics (move, trail, collision)       │
│    ├─ Capture (flood-fill loop closing)     │
│    ├─ Renderer (canvas 2D draw pipeline)    │
│    ├─ Audio (WebAudio synth: pop/kill/death)│
│    └─ GameLoop (fixed dt + rAF)             │
└─────────────────────────────────────────────┘
```

## 2. CORE DATA
- `grid = Uint8Array(100*100)` → 0=neutral, 1..15 = player/bot id
- `trailGrid = Uint8Array(100*100)` → 0 none, id trail owner (for O(1) cut detection)
- Positions float (x,y) in cell units; cell = floor(pos)
- Fixed timestep: dt = 1/60; accumulator; render interpolation

## 3. CAPTURE ALGORITHM (exact Paper.io2 feel)
1. Player territory me enter hua → trail polyline close ho gayi
2. Trail + territory intersection ke along boundary path banao
3. Flood fill from map corners (outside region mark karo)
4. Jo cells outside mark nahi hue + neutral/enemy → **player ka**
5. Trail cells bhi player territory ban jate hai
6. Ek capture me enemy ka pura territory bhi loot ho sakta hai

## 4. COLLISION RULES (priority order, per frame)
```
if cell owner trail != 0 and != me → I AM CUT → I die
if my cell==wall boundary → I die
if enemy inside own territory → can't be killed (invulnerable)
```
- Trail-to-trail: jo trail mere head se touch ho mere head ke pehle → main cut hua
- Head-on dono bahar: dono dead (rare, same-frame resolution)

## 5. BOT AI STATE MACHINE
```
IDLE → pick target direction
EXPAND → territory se bahar, loop shape bana (semi-circle/rect path)
RETURN → territory ki taraf steer
AVOID → wall ya stronger trail near → emergency turn
AGGRESSIVE → (30% bots) enemy trail ke toward intercept
```
- Steering: desired angle → current angle me lerp (turn rate limit)
- Safety check: 6-cell lookahead, trail/wall → turn

## 6. RENDER PIPELINE (60fps)
1. Clear + camera transform apply
2. Grid background (batch lines)
3. Territories (cell scan, merge same-color horizontal runs → fewer rects)
4. Trails (thick polylines, round-cap)
5. Players (rounded square + eyes toward dir)
6. Particles (death pop)
7. HUD update (DOM, 4×/sec throttle)

## 7. CAMERA
- pos lerp: `cam += (target-cam) * (1 - 0.92^dtFrames)`
- zoom: area-based `zoom = clamp(base * sqrt(myCells/2000), 0.5, 1.4)`
- death/menu: zoom-out cinematics

## 8. AUDIO SYNTH (WebAudio, no files)
| Event | Synth |
|---|---|
| capture small | sine 600→900Hz, 90ms, gain 0.2 |
| capture big (>=300 cells) | sine 400→1200Hz sweep + noise burst |
| kill | square 200Hz, 60ms + click |
| death | saw 300→80Hz, 400ms descend |
| click UI | short blip |

## 9. SAVES
- localStorage: `paperio2_best` (best %), `paperio2_skin`

## 10. CONTROLS
- Mouse: player cursor ki direction me steer (angle from screen center)
- WASD/Arrows: 8-way target angle
- Touch: drag joystick
- Enter/Space in menus

## 11. RESPONSIVE
- Canvas = window size × devicePixelRatio
- HUD font scales via clamp()
- Min canvas 320px support

## 12. PERFORMANCE BUDGET
- 100×100 grid = 10k cells; per-frame full scan ok (typed arrays)
- Territories: run-length horizontal rect merging
- Bots: 12 max; AI tick 10Hz (physics 60Hz, decisions 10Hz)
- GC: no per-frame allocs (reuse arrays, object pool for particles)

## 13. MATCH FLOW
```
MENU → countdown 3,2,1 → PLAYING → (death) → GAMEOVER screen (score, best, revive=play again)
```
- Win at 100% → confetti surprise screen
