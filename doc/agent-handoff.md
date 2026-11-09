# Agent handoff: Create: Airwaves revival

Last updated: 2026-11-09. Status: **Phase 0 in progress. The mod builds and the dev server loads (jar in `build/libs/`); waiting on the play-test.**

## The project
Revive **Create: Airwaves**, a radio addon for the Create mod. Its authors deleted it about 2 days after release, after being called out for using AI to write it.

What the mod does:
- You talk into a microphone and your voice goes down wires to a transmitter.
- Radios tuned to the same frequency hear it, with static that grows with distance, hills and storms.
- It also adds tape players, relays, jammers, a circuit workbench, cables, Ponder scenes and a field manual.

The user also wants new features, mainly **video broadcast to CRT TV screens**.

## About the user
- They have the jar and decompiled files but didn't know what to do with them.
- Explain things plainly and avoid jargon.
- They worry when wording sounds like a deadline or a risk, so be clear when something is optional or has no time pressure.
- Wants to **plan and discuss before building**. Get a go-ahead before each phase.
- No built-in microphone on this PC; a USB mic comes later. Test audio with the tape player, or with a second client, until then.

## Sources (GitHub)
| Repo | What it is |
|---|---|
| [liangforstudy/create-airwaves](https://github.com/liangforstudy/create-airwaves) | **The user's fork, where all work happens.** Forked from vigasilva/Create-Airwaves, so its history and credit are kept. These docs live here in `doc/`. |
| [vigasilva/Create-Airwaves](https://github.com/vigasilva/Create-Airwaves) | **Chosen base** (upstream of the fork). A reconstruction of 0.1.1 made with AI (Codex) by a community member, not the original author. It uses a normal Gradle layout, and dependencies download from public Maven repos. Branch `master`. |
| [TheStuffThatIsUnknown/Create-Airwaves-Decompiled](https://github.com/TheStuffThatIsUnknown/Create-Airwaves-Decompiled) | Raw 0.1.0 decompile (`.class` files plus assets). Reference only. Branch `0.1.0`. |
| [DJP728/Create-Airwaves-Archive](https://github.com/DJP728/Create-Airwaves-Archive) | Original `airwaves-1.21.1-0.1.1-atm10.jar`. |

## License
- 0.1.1 jar declares **CC BY-NC-SA 4.0** (0.1.0 declared MIT). Follow the stricter one.
- Credit the original authors, no commercial use (likely rules out the CurseForge rewards program), and keep the same license.
- Don't present the revival as the official mod.

## Versions and dependencies
- **Minecraft 1.21.1, NeoForge only.** The reconstruction builds against NeoForge 21.1.248; the mod requires 21.1.0 or newer.
- **Required:** Create `[6.0.10, 6.1.0)`, Create: Aeronautics, Sable.
- **Optional:** Create: Power Grid (0.6.2+; it in turn needs Sable 2.0.3+ and Architectury 13.0.8+), Create: Electro Energetics, Simple Voice Chat, JEI.
- **Build-time:** Ponder, Flywheel and Registrate come bundled inside Create for players.
- Build: Java 21 + `./gradlew build`; output goes to `build/libs/`.

## Code notes (reconstruction)
- **Package:** `com.airwaves`. Folders:
  - `be/` block entities
  - `block/`
  - `logic/`: `RadioNetwork`, `TapeAudio`, `SoundCapture`, `Cables`
  - `net/` network payloads, e.g. `RadioAudioPayload`
  - `client/`: `AudioPlayback`, `VoiceClient`, `MuLaw`
  - `compat/`: `sable`, `svc`, `powergrid`, `cee`, `create`
- **Built-in voice:** the mod has its own voice system (mu-law audio over its own payloads). Simple Voice Chat support is optional, under `compat/svc`.
- **Signal quality:** receivers already compute a `quality` float, see `SpeakerBE` / `AudioSink`. Reuse it for TV static.
- **Aeronautics/Sable:** Sable support is already kept separate in `compat/SableCompat`, so making both optional should be feasible.

## Decisions made
- Base: **vigasilva/Create-Airwaves**, forked as **liangforstudy/create-airwaves** (done).
- **Mod ID stays `airwaves`**, so worlds made with the original still load.
- The fork exists on GitHub. Publishing a release (e.g. on Modrinth/CurseForge) comes **later**, not now.
- Display name is open. The user leans toward "Airwaves" + Extended / Enhanced / TV Edition.

## Phase plan
- **Phase 0: get it running.**
  - Build the code that's already in the fork.
  - Launch a dev client.
  - Check the main features: tape player → transmitter → radio/speaker, cables, Ponder.
  - Expect some bugs, since the reconstruction is AI-made from decompiled code.
- **Phase 1: cleanup.**
  - Make Aeronautics and Sable optional dependencies.
  - Rename the display name.
  - Add credits and the license.
- **Phase 2: video to CRT TVs.**
  - Full plan and open questions are in [phase-2-video-plan.md](phase-2-video-plan.md).
  - Recommendation: video tapes/slides in v1, a camera block in v2. Skip desktop/webcam streaming.
  - No decision is needed before Phases 0–1. If it's still open after Phase 1, stop and wait for the user.

## Open questions (for the user)
See [phase-2-video-plan.md](phase-2-video-plan.md):
1. Video source for v1.
2. Resolution and frame rate.
3. Colour vs. limited palette or black and white.
4. How images and clips get onto tapes.
5. Camera rendering on a player's client.
6. Mod name.

## PC setup
- Prism Launcher: already installed (instances in `D:\PrismLauncher\instances`).
- Java 21: confirmed (`D:\Downloads\jdk-21.0.2`; Gradle also has a Temurin 21 under `~/.gradle/jdks`). `java` on PATH is still 17, so builds need `JAVA_HOME` pointed at a 21 JDK.

## Next step
Play-test the built jar on the PC (Phase 0 step 5 checklist in [phase-0-plan.md](phase-0-plan.md)).
