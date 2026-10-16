# RUT

An original, single-screen dirt truck racing game. Open `index.html` directly in a modern browser. No installation, server, network access or assets are required.

## Full requested game specification

Canvas 960x720 scaled to fit window, 60fps, fixed 1/60 timestep. One dirt track fully visible on a single fixed screen (mud patches, bumps, start/finish straight); 8 tracks of increasing difficulty defined as DATA (centreline point lists, some mirrored). Surface detection by sampling an offscreen canvas under each truck (track/offroad/mud/wall). 4 trucks (1 player + 3 AI), 4 laps, slidey arcade physics with drift, mud slowdown, wall bounce, truck bumps. SPACE = nitro. Pickups: N = nitro refill, $ = cash, respawning on a timer. AI: waypoint centreline following + slight rubber-banding + basic avoidance. Controls: arrows/WASD + on-screen touch buttons. Loop: title -> race (countdown; HUD lap/position/standings/speed/nitro/cash) -> results with prize money -> upgrade shop (Top Speed, Acceleration, Grip, Nitro Tank; 5 levels each, rising prices) -> next race. Season of 8 races; ends early after 3 consecutive last-place finishes; season summary (races, wins, podiums, earnings) + New Season reset. WebAudio engine/effects + mute toggle. Comment the code with a clearly marked TUNING constants section at the top of the script.

Implementation: one self-contained `index.html`, HTML5 Canvas and vanilla JavaScript; no external libraries, fonts, images or assets. All sound uses WebAudio. Original game name, generated title artwork and procedural race art, without any existing game's name, art or likeness.

## How to play

- **Up / W:** accelerate. **Down / S:** brake, then reverse.
- **Left / A** and **Right / D:** steer. Steering is relative to the truck's heading.
- **Space:** hold to use nitro. **N** pickups refill the tank; **$** pickups grant $45. Both respawn after 9 seconds and can be collected by any truck.
- **P:** pause/resume. Click or tap the track to resume. Losing focus pauses automatically.
- **M** or the sound button: mute/unmute. Audio starts after your first interaction.
- **Mobile:** touch anywhere on the left half of the game canvas to spawn a floating joystick. Drag toward the desired heading; distance sets throttle. Centre the stick or release to coast. Pull against the truck’s nose to brake and then reverse. The base stays at the initial contact and the knob follows your thumb, capped at full throttle.
- **Nitro:** touch and hold the right half of the game canvas with another finger. Steering and nitro track separate pointer IDs, so you can use both together. Fingers retain their original roles when crossing halves. Release/cancellation clears that finger; pausing, focus loss, and resizing clear all gestures.
- The joystick uses a 58 CSS-pixel radius and 12% centre deadzone; both are adjustable in TUNING. Its base and knob are Canvas drawings; no DOM driving buttons remain.

Follow the circuit through every checkpoint in order and cross the checkered straight to complete each lap. The orange-and-bone Oxide truck and orange marker identify the player. Mud reduces speed and grip, offroad slows the truck, and raised strips kick the suspension. Rocks and perimeter barriers bounce trucks; trucks can bump one another.

The race concludes when the player completes four laps. Trucks that already finished rank by their finish time; remaining trucks rank by circuit progress. Results show unfinished drivers as “Still racing.” Prizes are $600 / $390 / $220 / $100. Pickups and prizes count toward season earnings; spending does not subtract from that statistic.

Between races, spend cash on five purchasable levels per upgrade. Each starts at $140 and rises by a factor of 1.75 per level (rounded). Top Speed adds 8% of base speed per level; Acceleration adds 14%; Grip adds 16%; Nitro Tank adds 0.8 seconds to its initial 4 seconds. Nitro refills at each race start. AI pace increases across the season while tracks narrow and hazards increase. Three consecutive fourth-place finishes end the season early. Finishing race eight also opens the summary. **New Season** resets cash, upgrades, results and statistics. Season progress lives in memory for this play session; top-times records persist separately in localStorage.

## Implementation notes

All tuning constants appear in the clearly marked **TUNING CONSTANTS** section at the start of the script. `TRACKS` contains centreline point lists, widths, mud and bump indices, plus mirrored-track source references. The display canvas is 960 × 720 with a fixed camera and proportional window scaling. An offscreen color-coded surface canvas is sampled for truck collision and terrain physics; a separate offscreen canvas caches the procedural artwork. Simulation uses a 1/60-second accumulator with a frame-delta cap. Rendering follows the display refresh rate (60fps on a 60Hz display).

Audio consists of a continuous synthesized engine oscillator and short synthesized effect oscillators. All interface typography uses local system fonts. The entire game can run offline from the single HTML file.

## Visual identity

RUT uses an original screen-printed rally identity: burnt orange, bone, charcoal and sage, with tire-cut title lettering and a generated rally illustration embedded directly as a WebP data URI in `index.html`. The image requires no separate file or network request. Race art remains procedural Canvas drawing. Four numbered, two-tone liveries identify the field: Oxide (player, orange/bone), Tidal (Reed, turquoise/charcoal), Ember (Flint, vermilion/bone), and Dusk (Moss, lavender/charcoal). Detailed truck sprites are cached in canvases created by the same script. Cut-corner controls and pickups, a segmented nitro gauge, trackside stakes, clay textures, and a truck preview in the pit shed extend the visual system through the game.

The generated title art is decorative; title semantics, instructions, buttons, HUD, and game state remain native HTML or Canvas text. The complete game remains one standalone HTML file with no external dependencies.

## Control redesign (supersedes the original touch-button requirement)

The original requested specification above is preserved as supplied. The current mobile-led control scheme replaces every on-screen driving button with a floating heading-and-throttle joystick on the left half of the game canvas and hold-to-boost on the right half. Keyboard arrows/WASD and Space remain available on desktop. Menu and sound buttons remain ordinary interface controls.

## Fixed-view fun pack and MUDMAN palette

The camera is locked: the full circuit is always visible on the 960×720 canvas. Window scaling preserves its aspect ratio; there is no scrolling, camera following or zoom. The floating joystick and keyboard controls remain unchanged.

- **Jump ramps:** cross in the arrow/course direction above 65 speed units for 0.68 seconds of air time. Airborne trucks pass over props and other trucks. Landing on a firm surface, aligned with travel and without hitting a wall, grants a 22-unit speed bonus and a brief raised speed cap.
- **Oil slicks:** dark slicks trigger a short spin and very low lateral grip, with a cooldown to avoid retriggering each step.
- **Boost pads:** yellow chevrons provide a directional impulse and 0.8 seconds of increased maximum speed.
- **Cones and barrels:** ten destructible props per race. The player receives $10 per prop, with debris and audio feedback. AI can also destroy props; destroyed props do not pay out again and reset for the next race. Airborne trucks pass over them.
- **Water puddles:** teal with pale ripples, distinct from brown mud. They cause a mild slowdown and moving splash particles.

Ramp, oil, boost, water, cone and barrel types have distinct codes on the offscreen surface canvas. Smashing a prop restores the underlying surface from a cached base mask. All effects run within the fixed simulation timestep. Collectibles are spaced away from new hazards for readability.

Track scenery includes a MUDMAN start gantry, grandstand spectators, flags, banners, tyre stacks, gravel and tire ruts. Art and interface use Deep Mud `#4B2E1F`, Clay Brown `#8A5A3B`, Dust Tan `#C69B6D`, Nitro Orange `#FF6A00`, Turbo Yellow `#FFC928`, Track Teal `#1DA7A1`, Night Asphalt `#263238`, and Cloud White `#F3F1E8`. Existing embedded title artwork remains self-contained. No external assets, libraries or network requests are required.

## Landscape-first orientation

Play horizontally. In portrait orientation, the game and its controls are hidden behind a “Rotate your phone” prompt. Simulation and sound pause, held inputs are cleared, and the race resumes in landscape (a manually paused race stays paused). Rotation never resets the race or season. The fixed 960×720 canvas scales to the largest proportional fit in the landscape viewport using dynamic viewport height; wider screens retain side gutters so the entire track stays visible without cropping or distortion. HUD remains within the fixed canvas; menus size themselves to the fitted game surface, including short landscape phones.

## Selectable AI skill

Choose **Rookie**, **Pro**, or **Ace** on the title screen before starting a season. Rookie is the default. The chosen profile is shown in the HUD, garage, results and summary and stays fixed through the season. **New Season** retains that choice while resetting race progress, money and upgrades. **Choose skill** on the summary returns to the title selector.

| Profile | Base AI speed multiplier | Steering gain / grip multiplier | Mistake chance per second | Rubber-band cap | Nitro behavior |
| --- | --- | --- | --- | --- | --- |
| Rookie | 0.77 | 1.85 / 0.93 | 6.5% | ±7% | Short 0.35s bursts when well behind; 8s cooldown |
| Pro | 0.89 | 2.20 / 1.08 | 2.8% | ±4% | 0.7s bursts when chasing; 5s cooldown |
| Ace | 0.99 | 2.65 / 1.22 | 0.8% | ±1.5% | 1s bursts on safe straights, including from the lead; 3s cooldown |

All profiles share the same physics, track hazards, pickups, collision rules, fuel limits and ordered lap gates as the player. Race progression and small driver differences adjust their base speed modestly. Rookie has wider steering variation and larger, more frequent brief driving errors; Ace follows its target more precisely, accelerates harder, and makes smaller errors. Nitro is suppressed while spinning, airborne, making an error, or turning too sharply. Skill parameters live together in `AI_SKILLS` beside the tuning constants.

Automated Chromium checks covered all three AI drivers finishing all eight tracks at all three skills. Season-flow checks exercised ordered player lap gates, prizes, upgrades, eight-race summaries, three-last-place early exits, new-season reset and returning to the skill selector at each level. Those flow checks use controlled checkpoint placement; AI completion checks run the actual driving simulation. Human difficulty balance remains a playtesting judgment.

## Top-times boards

Use **Top times** on the title or results screen to browse a separate board for each of the eight tracks, including mirrored layouts. Each board keeps the five fastest player finishes across all AI skills, showing rank, player name, complete four-lap race time (`minutes:seconds.milliseconds`), and skill. Lower times rank higher; an equal time does not displace the oldest record when the board is full.

After a qualifying finish, enter a name (1–20 characters) and choose **Save time**, or **Skip** to continue without recording it. The last saved name is offered next time. Nonqualifying finishes proceed directly to results. Viewing boards and returning to results does not award prizes again or interrupt season progression.

Boards and the last entered name are stored in the browser's localStorage under `mudman.topTimes.v1`. They survive reloads, browser restarts and New Season on the same browser/origin, unless browser data is cleared. Storage is validated on load, names are escaped for display, and unavailable storage falls back to session-only records with a notice on the board. A copied file, different browser/origin or private session may have separate storage.
