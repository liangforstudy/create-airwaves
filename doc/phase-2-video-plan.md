# Phase 2 — Video broadcast to CRT screens

Status: **draft, not started.** Phases 0 (get the reconstruction building) and 1 (cleanup, optional dependencies, rename) come first.

Base: [liangforstudy/create-airwaves](https://github.com/liangforstudy/create-airwaves), a fork of [vigasilva/Create-Airwaves](https://github.com/vigasilva/Create-Airwaves) (reconstructed 0.1.1, NeoForge 1.21.1, Create 6.0.x).
License: CC BY-NC-SA 4.0, inherited from the original. Credit the original authors, no commercial use, same license for derivatives.

## Goal

Send video over the existing Airwaves radio network and show it on CRT TV blocks. Picture quality follows the same signal model the audio already uses: distance, terrain, storms and jammers.

## Planned pieces

### CRT TV block
- Small screen blocks that join into a bigger screen when placed side by side, like Create's display boards.
- Tune it like a radio, by frequency or frequency card.
- Powered the same way other Airwaves devices are (rotation, or electricity if Power Grid is installed).
- Optional: a TV-sized speaker, so a TV plays the audio on its channel too.

### Signal-quality effects
- Reuse the existing `quality` value that receivers already compute (see `SpeakerBE` / `AudioSink`).
- As the signal weakens: snow/static noise, then horizontal roll and colour loss, then no picture.
- Do this in a client-side shader/overlay so the server only sends one number per screen.

### Transport (how frames travel)
- Low resolution: around 128×96 pixels, 5–10 frames per second.
- Limited colour palette (e.g. 16–64 colours) with dithering.
- Only the changed tiles of the picture are sent each frame, and they're compressed.
- Rough budget: under ~10–20 KB/s per active channel.
- New network payload alongside the existing `RadioAudioPayload`. The server relays frames only to players who have a tuned TV nearby.

### Video sources (the main decision)

| Option | What it does | Difficulty | Notes |
|---|---|---|---|
| **A. Camera block** | Live view of the world from where the camera stands | Hard | A Minecraft server can't draw images, so a nearby player's game must render the camera view and send it up. That costs that player some performance. Need rules for which player renders it and what happens when nobody is near. |
| **B. Video tapes / slides** | Load an image or short clip onto a tape item and play it from a tape player | Medium | Reuses the existing tape system. Needs a way to import images/clips: a folder on the server, or an in-game tool. |
| **C. Desktop / webcam streaming** | Stream a real screen or webcam | Hard | Recommended to skip: privacy problems and a lot of extra work. |

**Recommendation:** B in version 1 and A in version 2. B builds and tests the screen, transport and static effects. A then only adds a new source.

### Later ideas (not committed)
- Redstone / Create signals carried over radio.
- TV channel guide shown on Create display boards.
- Record button on TVs: save a broadcast onto a tape.

## Open questions

1. **Video source for v1:** B (tapes/slides) first, or A (camera) straight away?
2. **Resolution and frame rate:** is ~128×96 at 5–10 fps acceptable, or should it be more retro (64×48) or sharper?
3. **Colour:** full colour, or a CRT-style limited palette (could also offer black-and-white TVs as an early tier)?
4. **Image/clip import for tapes:** server folder only, or an in-game way to load them?
5. **Camera rendering (if A):** is it acceptable for a nearby player's game to do the rendering?
6. **Mod name:** candidates are "Create: Airwaves Enhanced", "Create: Airwaves Extended" and "Create: Airwaves TV Edition".
## Decided so far
- Mod ID stays `airwaves`, so worlds made with the original mod still load.
- Forked on GitHub as liangforstudy/create-airwaves. Publishing a release comes later, not now.
- Testing note: this PC has no built-in microphone. A USB mic will be added later. Until then, test audio with the tape player and with a second player or client.
