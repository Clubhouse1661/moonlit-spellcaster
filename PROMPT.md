# Prompts

Moonlit Spellcaster was built with Claude (Claude Code) from two prompts, reproduced verbatim below.

Shareable image versions are in [`prompt-cards/`](prompt-cards/).

## 1. Original prompt, by Majid Manzarpour

The starting point: an animated (not yet playable) pixel art wizard casting a spell.

> Create a single self-contained HTML file that renders an animated pixel art wizard casting a spell, using vanilla JavaScript and Canvas 2D. No external assets, libraries, or network requests.
>
> RENDERING
> - Draw everything to an offscreen canvas at a fixed logical resolution of 128x96, then blit to a fullscreen display canvas scaled by the largest integer factor that fits the window, centered, with imageSmoothingEnabled = false and CSS image-rendering: pixelated.
> - All drawing snaps to integer coordinates on the logical canvas. No sub-pixel positions, anti-aliasing, gradients, or shadowBlur.
> - Fixed palette of ~24 hex colors: deep blues/purples for night sky, warm robe tones, 3-4 bright magic colors. Every pixel comes from this palette.
>
> CHARACTER
> - Build the wizard procedurally from filled rects and pixel runs, ~24x32 logical pixels: pointed hat with a bend, long beard, two-shade robe with darker outline, staff with a gem at the tip.
> - Parameterize the pose (staff angle, arm raise, head tilt, robe sway). Animate parameters smoothly, then quantize to the pixel grid each frame so motion reads at an 8-12 fps pixel animation feel even though the loop runs at 60fps.
>
> ANIMATION
> - Looping state machine: IDLE (2-frame bob, beard sway) -> CHARGE (staff raises, gem flickers, sparks spiral inward) -> CAST (bright burst, projectile fires across the scene, 1-2 pixel screen shake) -> RECOVER (settle back). Ease pose parameters between keyframes.
> - Pooled allocation-free particle system: preallocate and reuse. Sparks orbit the gem during CHARGE, explode outward on CAST, each particle stepping its palette index from white to magic color to dark before despawn. Snap particle positions to the grid when drawing.
> - Fixed 60hz timestep update with rAF rendering. Zero object allocation inside the loop.
>
> SCENE
> - Minimal background: dark sky, a few twinkling 1px stars, moon, stone floor line. Character silhouette must read clearly.
> - Subtle 1px rim light on the wizard from the gem, brightening during CHARGE and CAST.
>
> QUALITY BAR
> - Crisp pixels at any window size, seamless loop, stable 60fps, readable silhouette. Should look like a polished 16-bit sprite animation, not vector shapes scaled down.

## 2. Follow-up prompt

Extends the animation into the small playable game in this repo. It was written against the first version, which was published as a Claude artifact, hence the last line.

> Great result. Let's extend the Moonlit Spellcaster into a small playable scene, reusing the same file, palette, 128×96 resolution, fixed 60 Hz timestep, 10 fps pose cadence, and zero-allocation loop.
> 1. Generalize the rig. Refactor `buildSprite()` so it's driven purely by pose parameters plus a `facing` flag (±1) that mirrors the sprite horizontally, including the hat bend, staff, and rim light. Add any new parameters walking needs (for example, front/back boot offsets, hem swing, and body bob) without changing how the existing idle/charge/cast poses look.
> 2. Walk cycle. Add a 4-frame walk cycle (contact, down, passing, up) at the same 10 fps cadence:
>
> * Boots alternate and lift 1px on the passing frames.
> * The robe hem swings opposite to the lead foot.
> * The beard and hat tip trail slightly behind the direction of travel.
> * The staff works as a walking stick, planting on the contact frames.
>
> Walking speed should match the foot plants so the feet don't slide. Ease between idle and walk over 1–2 frames.
> 3. Controls and states. Replace the auto-loop with player input:
>
> * ←/→ or A/D to walk, turning to face the direction of travel.
> * Hold Space to charge (the longer the charge, the bigger the bolt, up to a cap). Release to cast in the facing direction.
> * Tapping Space gives a quick weak cast.
> * While charging the wizard can't walk, or walks at half speed (pick whichever looks better).
> * Keep the RECOVER settle after a cast.
> * Add large on-screen touch buttons (left, right, cast) for mobile, drawn in pixel style on the same logical canvas.
>
> 4. Scene. Widen the level to about 3 screens with a camera that follows the wizard and snaps to whole pixels. Add parallax to the stars, moon, and hills, and tile the stone floor. Place 2–3 simple pixel targets (for example, floating wisps or training dummies) that the bolt hits with a small particle burst, then respawn. Stop the wizard at the level edges.
> 5. Quality bar. Same as before: crisp integer scaling, a readable silhouette in both facing directions, stable 60 fps, and no allocation in the loop. Before finishing, confirm in the browser that walking, turning, charging, and casting all work, then republish to the same artifact URL.
