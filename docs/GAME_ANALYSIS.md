# PAPER.IO 2 — COMPLETE GAME ANALYSIS
## (Har choti cheez ka deep breakdown)

---

## 1. GAME OVERVIEW
- **Name**: Paper.io 2 (VOODOO)
- **Genre**: Arcade / Territory-capture .io game
- **Core loop**: Territory se bahar niklo → trail draw karo → territory touch karo → area capture
- **Feel**: Smooth, satisfying, colorful, minimal

---

## 2. CHARACTER (PLAYER) — MICRO BREAKDOWN
| Element | Detail |
|---|---|
| Shape | Square (rounded corners ~25%) |
| Size | ~2 grid cells visual, hitbox slightly chhota |
| Eyes | 2 white circles + black pupils, center-aligned |
| Eyes direction | Pupils look toward movement direction |
| Skin/Color | Solid fill color + darker outline (1px) |
| Face | Cartoonish — faceless except eyes |
| Trail | Peeche chalta hai, same color, semi-darker |
| Rotation | Body movement ke saath rotate nahi hota (sirf eyes shift) |
| Skins | 100+ skins (smiley, angry, cute, animals) — flat 2D style |
| Death | Small pop particle burst + color fade |
| Kill animation | Victim ka territory grey/neutral ho jata hai |

---

## 3. MAP / ARENA — MICRO BREAKDOWN
| Element | Detail |
|---|---|
| Shape | Square/rectangular arena |
| Size | 100×100 grid cells ( approx ) |
| Background | Light grey/white with faint grid pattern |
| Grid | Faint lines, visible when territory hai |
| Border walls | Dark colored edge — touch = instant death |
| Player territory | Solid color block, sharp edges |
| Enemy territory | Solid colored blocks of other colors |
| Trail visual | Ribbon jaise, same color but darker shade |
| Camera | Follows player smoothly (lerp ~0.08/frame) |
| Zoom | Dynamic — territory kam ho to zoom in, zyada to zoom out |
| Minimap | Nahi hai Paper.io 2 me (fullscreen arena dikhta hai) |

---

## 4. CORE MECHANICS (PHYSICS)
### Movement
- Constant speed (~4 cells/sec), joystick/direction se steer
- Smooth turning (lerp), instant nahi — arc me ghumta hai
- Web version: mouse/arrow keys direction
- Turning radius smooth — arcade feel

### Trail Rules
1. Jab tak apni territory me ho → trail nahi banta
2. Territory se bahar → trail start (last exit point se)
3. Territory me wapas aao → loop close → **enclosed area capture**
   - Capture rule: Sab territory enclosed by loop → player ka (chahe enemy ka ho ya neutral)
4. **Trail cut = DEATH**: enemy trail ya apni hi trail cross karo → dead
5. **Wall touch = DEATH**: arena boundary se cross nahi kar sakte

### Capture Math
- Grid-based flood fill: loop ke andar ka sab kuch capture
- Chahe enemy territory enclose ho — wo capture ho jata hai
- Sabse bara risk = jab bahar ho (sirf trail vulnerable)
- Line join → flood fill from outside → jo outside reh gaya wo capture
- Trail-closing wali side se closed path banata hai

---

## 5. AI BOTS (ENEMIES)
| Behavior | Detail |
|---|---|
| Count | ~10-15 bots active |
| Spawn | Random, small starting territory ke saath |
| States | EXPAND (bahar jakar loop bana rahe), RETURN (territory wapas), IDLE |
| Intelligence | Low-medium: random direction changes, wall se turn |
| Kill detection | Agar bot ka trail player ke cross ho jaye → player kill |
| Bot death | Player bot ke trail ko cut kare → bot dies, territory neutral |
| Respawn | Dead bot kutch der baad naya spawn, new color |
| Difficulty | Over time bots slightly aggressive (homing toward player trail) |

---

## bots me ~5-6 different personality:
- **Explorer**: lamba loop, darte nahi
- **Cautious**: chhota loop, jaldi wapas
- **Aggressive**: player ke trail ki taraf attack
- **Idiot**: random wander
- **Greedy**: enemy territory enclose karne ki koshish
- **Homebody**: apne territory ke aas paas raho

---

## 6. UI / HUD
| Element | Detail |
|---|---|
| Score | Top-left, "Score: 4.2%" style percentage |
| Leaderboard | Top-right, top-5 players + percentage |
| Kill feed | Nahi hota (minimal UI) |
| Death screen | "You were killed by [name]!" + REVIVE / PLAY AGAIN
| Start screen | Logo + TAP TO PLAY + skin selector
| Skins | Before game start, pick karo |
| Buttons | Small, flat colored buttons, rounded corners

---

## personalities (botkeb ke niche ~3-6 |
| Bot count tuning | Slow ramp 10→15 |

---

## 7. AUDIO (WEB REPLICA KE LIYE)
| Sound | Timing |
|---|---|
| Capture chhota | Soft pop |
| Capture bara | Bigger pop + subtle whoosh |
| Death | Descending tone |
| Kill | Satisfying crunch |
| Music | Nahi (silent game) |
| (Web version me hum WebAudio se synth karenge) |

---

## 8. COLOR PALETTE (SIGNATURE LOOK)
| Purpose | Color |
|---|---|
| Neutral bg | `#E8ECEF` light grey |
| Grid line | `#D0D5DA` faint |
| Player default | `#2196F3` blue |
| Bot colors | 10+ vivid: red, orange, yellow, green, teal, purple, pink |
| Territory fill | Base color, ~8% darken border |
~ 10%
| Wall | `#2C3A47` dark slate |

---

## 9. SCORE SYSTEM
- Score = (player territory cells / total cells) × 100%
- Leaderboard rank by territory % descending
- Kill = +bonus? Paper.io 2 me direct percentage nahi badhta, territory %hi score hai

---

##  dominate DOMINATE THE WORLD mode
- Countries map pe hota hai — abhi v1 web me skip, baadme add
- Best score localStorage me save

---

## 10. DEATH CONDITIONS (EXACT RULES)
1. Own trail cross → dead
2. Enemy trail cross → dead (enemy ka trail tumse cut ho jaye jab tum dono bahar ho)
3. Wall touch → dead
4. Head-on collision dono bahar → jo p

---

## Bana hai — dono dead
5. Head-on inside territory → attacker (territory owner) wins? Nahi — Paper.io2 me inside collision → dono survive, pushed back

### Kill rule simple version:
- **Tumhare trail ko koi bhi cross kare (tum bahar hue) → TUM dead**
- **Tum kisi ka trail cross karo → wo dead**

---

## 11. DEAD PLAYER DROP
- Territory + trail fade out (grey)
- Bots ka territory: capture karo to bonus feel
- Best score localStorage

---

## 11. RANK / LEADERBOARD LOGIC
- Rank = territory % descending
- Top 5 list live update, 2 sec me refresh
- Player highlight (b

---

## chotee chotee सुधार
Best score localStorage me save — hamesha dikhna chahiye.

---

## 12. ANIMATION / JUICE
| What | Detail |
|---|------ doclet
|---|
| Trail drawing | Chunky ribbon, sharp corners (miter join) |
| Capture | Region flash → settle |
| Zoom | Smooth lerp on area change |
| Camera | Lerp follow |
| Death pop | ~12 particles, random radial, fade 400ms |
| Kill flash | Screen edge flash subtle |
| Join anim | Round join between trail and territory |

---

## 12b. PERF NOTES
- 100×100 grid → 10k cells → flood fill is cheap (10k visits max)
- Canvas 2D with dirty-rect updates possible; full redraw OK at 60fps
- Cells offscreen skip karo
- requestAnimationFrame + fixed timestep (accumulator)

---

##  STATE MACHINE
- BOOT → MENU → PLAYING → DEAD → (MENU | PLAYING)
- MENU me preview render (idle bots) as background

## 13. STATE MACHINE
```
MENU (start screen, skin pick)
  └─ PLAYING
       ├─ player dead → GAMEOVER (death screen)
       ├─ capture 100% → WIN (secret surprise!)
       �100% → WIN SURPRISE (confetti + "YOU DOMINATED THE WORLD!")
```

## 14. CONTROLS (WEB)
- Mouse move → direction (cursor follow)
- Arrow keys / WASD → 4-direction steering (smooth lerp turn)
- Touch: drag = virtual joystick
- P / Esc → pause
- Space → pause overlay

## 15. TECH STACK (WEB)
- Vanilla JS + Canvas 2D (koi engine nahi — lightweight)
- Single HTML file me sab (CSS+JS inline) — file:// se bhi chalega
- WebAudio synth sounds (no files)
- localStorage best score
- Fixed timestep + rAF
- Camera + zoom lerp

## 16. FILE STRUCTURE
```
explabs/
  index.html      ← explabs landing (existing)
  paperio2/
    index.html    ← GAME (self-contained, single-file)
  docs/
    GAME_ANALYSIS.md  ← yeh analysis
    GAME_DESIGN.md    ← technical design
```
