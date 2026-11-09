# Phase 0 — Get it running

Status: **steps 1–4 done. The dev client runs; the field manual, Ponder and creative tabs were checked on 2026-11-09. A Numen companion driven by opencode built a test bench near spawn; the dev run now uses s00d/minecraft-mcp instead of Numen. Next: the audio half of the checklist (step 5), by ear.**

Goal: get the reconstructed mod (the fork [liangforstudy/create-airwaves](https://github.com/liangforstudy/create-airwaves), branch `master`, forked from [vigasilva/Create-Airwaves](https://github.com/vigasilva/Create-Airwaves)) to build, and confirm the main features work in a game. No new features and no cleanup in this phase. That's Phase 1.

## Who does what
- **Claude (in the cloud):** downloads the code, builds it and fixes build errors. Can't open Minecraft.
- **You (on your PC):** play-test the mod in Minecraft and say what works and what doesn't.

## Steps

### 1. Bring in the code ✅ done
- The user forked vigasilva/Create-Airwaves as liangforstudy/create-airwaves, so the code and its history are already here.
- The existing README already says it's unofficial and keeps CC BY-NC-SA 4.0. A note about this revival and the full credits come in Phase 1.

### 2. Build it (Claude) ✅ done
- The 1050 already has Java 21.
- Run `./gradlew build`. The first run downloads Minecraft, NeoForge, Create and the other parts it needs, which takes a while.
- If it fails, fix only what stops the build, and note each fix.
- Result: a mod file in `build/libs/`, e.g. `airwaves-1.21.1-0.1.1.jar`.

Fixes made (2026-11-09, on the user's PC, Windows, Java 21):
- `gradle.properties`: removed `org.gradle.java.home=/usr/lib/jvm/java-21-openjdk` (a Linux path that breaks any non-Linux machine); it is now a commented-out example.
- `data/airwaves/recipe/tape.json`: output count 2 → 1. The tape item is `stacksTo(1)`, so the recipe failed to load with "stack size of 2 was larger than maximum: 1".
- `build.gradle` dependencies: Power Grid `0.6.1` → the NeoForge 1.21.1 build of `0.6.2` (pinned by Modrinth version id `jCkPHjmP`; the version *number* resolves to the 1.20.1 Forge jar, which NeoForge refuses to load). Power Grid 0.6.2 needs Sable 2.0.3+ and Architectury; so Sable `2.0.1` → `2.0.6`, and `architectury-api:13.0.11+neoforge` added.

### 3. Quick automatic check (Claude, optional) ✅ done
- `./gradlew runServer` on this PC loads all mods and reaches "Done (1.6s)" with no mod-loading issues and no recipe errors.
- Power Grid's optional native solver prints a harmless `UnsatisfiedLinkError`; it falls back to the Java solver.
- The mod reports version `0.0NONE` in a dev run only, because `neoforge.mods.toml` uses `${file.jarVersion}` and a dev run has no jar. The built jar is fine.

### 4. Set up your PC (you, with simple steps from Claude) ✅ done for the dev run
- Install Java 21, if you don't already have it. (Prism Launcher is already installed.)
- Make a Minecraft 1.21.1 NeoForge instance in Prism.
- Add the mods to it:
  - Create 6.0.10 or newer (below 6.1)
  - Create: Aeronautics
  - Sable
  - the Airwaves jar from step 2
- Optional extras: JEI, Simple Voice Chat, Create: Power Grid.

#### The play-test where an llm drives the game: 
**Now: s00d/minecraft-mcp** (replaced Numen on 2026-11-09, because Numen's companion can't click screen buttons or hear anything, and most of the checklist needs both).
- [s00d/minecraft-mcp](https://github.com/s00d/minecraft-mcp) 0.6.0, NeoForge 1.21.1 jar from its GitHub release, in `libs/` (gitignored) and added to `build.gradle` as `runtimeOnly files(...)`.
- It runs inside the client and the agent plays as you, not as a companion. 129 tools, including `screen.widget.click`, `sound.events` (what played, where, subtitle), `screenshot.take` and `command.run`.
- Endpoint: `http://127.0.0.1:25580/mcp`, no token by default (loopback only, web pages refused). Config: `run/config/minecraft-mcp.toml`.
- Registered as `minecraft` in Claude Code (local scope for this project) and in opencode's `opencode.json`. Start the game first, then the agent.
- Also in the dev client, test-only: FerriteCore, ModernFix, Spark, ImmediatelyFast, EntityCulling. Not Dynamic FPS: it throttles the game while unfocused, which is exactly when an agent drives it.

**Before (Numen, no longer loaded):** the notes below are kept for reference. The jar is still in `libs/` and opencode's `numen` entry is disabled, not deleted.

**Numen** (CurseForge, Fabric + NeoForge, 1.21.1) is the mod that hooks an LLM up to this version of Minecraft. It spawns a real server-side player companion and, since 0.0.6, runs a built-in MCP server, so an external agent (Claude Code, Claude Desktop, Codex, Cursor) is the brain and the companion is the hands.
- Needs the Numen jar only; the external-brain mode uses the agent's own subscription, no separate API key.
- Claude Code 2.1.317 is on this PC at [censored]; it supports HTTP MCP. It was not used in the end: opencode drives the companion.

How it is actually wired (2026-11-09):
- The jar (`numen-neoforge-1.21.1-0.1.2.jar`, CurseForge file 8735972) sits in `libs/`, which is gitignored, and `build.gradle` adds it as `runtimeOnly files(...)` so it loads in `runClient`/`runServer` only. It is not part of the built jar.
- The MCP server reads `run/config/numen/mcp_server.json` at start-up, so it can be configured before launch: `enabled`, `host` (default `127.0.0.1`), `port` (default `8765`), `token`, `call_timeout_seconds` (default 300), `hidden_tools`, `quiet_fallback`. No in-game clicks are needed — the settings page only rewrites this file.
- Endpoint: `http://127.0.0.1:8765/mcp`, header `Authorization: Bearer <token>`, JSON-RPC 2025-06-18, POST only (a GET returns 405). Unauthenticated calls get 401.
- Agent side: opencode's `mcp.numen` entry, `type: remote`, `oauth: false`, `timeout: 120000` (its 5 s default is too short for some calls).
- Order matters: the server only listens while the game runs, so start the game first and opencode second. If opencode starts first it shows `server unavailable` and needs a restart.
- The token is in `run/config/numen/mcp_server.json` and in the agent config; it is deliberately not copied into this doc.
- `create_companion` works over MCP, but only once a world is loaded (a main-menu session answers `initialize` and `tools/list`, then refuses to summon).

### 5. Play-test checklist (you)
Use a creative test world. No microphone is needed for this.
- [x] The game starts, and the Airwaves items show in the creative tab or JEI.
- [x] The field manual opens (checked with the dev client on 2026-11-09; it shows `v0.0NONE`, expected in a dev run).
- [x] Ponder scenes open.
- [x] Tape player → cable → transmitter → radio on the same frequency: you hear the tape. (Creative transmitter and receiver; see "Session 2" below.)
- [x] Moving the radio farther away adds more static. (Measured as receiver signal quality, not by ear; see "Static over distance" below.)
- [x] A radio on a different frequency stays silent.
- [x] Relay and jammer behave sensibly. (Measured as receiver signal quality; see "Jammer" and "Relay tower" below.)
- [x] Circuit workbench opens, and crafting recipes appear. (Built a Radio Board with Auto; see "Screens" below.)
- [ ] Optional: a second client hears the transmission.
- [ ] Optional: an old world from the original mod still loads.

For anything broken, a sentence or a screenshot is enough. The game log helps if there's a crash.

#### Session 2 (2026-11-09, Claude Code over s00d/minecraft-mcp, same world)
Evidence is from the server's block data (`/data get block`) and the client's `sound.events` list, which gives each sound's id and position, so "the receiver played it" means a sound at the receiver's coordinates.

The bench opencode left: creative transmitter at (31, 68, −6) and creative receiver at (33, 68, −9), both with creative antennas, both on 98.1 FM; speaker (34, 68, −9); microphone (33, 68, −3); three tuning tables. Opencode's wires never connected (`links: []` on the mic and speaker). Added: a jukebox at (31, 68, −4) and a tape player at (32, 68, −4).

| Test | Result |
|---|---|
| Receiver picks up the transmitter | Pass: receiver `carrier: 1`, quality 1.0, before anything was wired. |
| Jukebox → microphone → wire → transmitter → receiver | Pass: `music_disc.cat` played at the receiver's position. Without the mic wire nothing was sent: the transmitter's "built-in mic" is for voice chat only; only a wired Microphone block picks up jukeboxes and game sounds (`RadioNetwork.onWorldSound`, `SoundCapture.pollJukeboxes`). |
| Receiver on 100.0 (set with `/data merge`, not the tuning table) | Pass: `carrier: 0`, nothing at the receiver. Back on 98.1: carrier and sound return. |
| Receiver → wire → speaker | Pass: speaker `on: 1`, sound moves to the speaker's position. |
| Tape player: load tape, press Record in its screen, Stop | Pass: tape holds 226 ticks (~11 s) with the disc's start/stop cues. |
| Tape playback (Play button) | Pass: plays from the tape player. |
| Tape player → wire → transmitter, started by a redstone pulse | Pass: plays at the tape player and over the air at the speaker. |

Found along the way:
- **Possible bug:** the transmitter's `on_air` block state stayed `false` the whole time it was broadcasting jukebox audio. It is only set by `TransmitterBE.onAir()`, which the sound-broadcast path does not seem to call. Check before Phase 1.
- The tape player screen draws its buttons by hand (no widgets), so an agent has to click by coordinates: GUI 427×240, panel at left 113, top 3; Play (176, 133), Record (212, 133).
- Wiring needs a clear line of sight to the block; a tuft of grass and an antenna both swallowed clicks.
- Inserting the disc with `/item replace` and placing blocks with `/setblock` were shortcuts; the wires, tape and screen buttons were done as the player.

##### Static over distance
The creative transmitter and receiver can't show static: the creative transmitter has unlimited range, and the creative receiver always reports quality 1.0 when it has a carrier. So this used regular blocks:
- **Transmitter** at (42, 70, −14), facing south, amethyst block in the chamber, copper block behind it at (42, 70, −15), 4 antennas on the copper, and a Create creative motor at (43, 70, −14) on its side shaft (16 RPM). It reported `active`, antenna 4, **range 862.5 blocks**. Tuned to 101.0 FM so the creative transmitter on 98.1 stays out of it. Its chunk is still force-loaded (`/forceload remove 42 -15` to undo).
- **Receiver**: a regular receiver with an amethyst block and one antenna on top, tuned to 101.0, copied with `/clone` to points due east of the transmitter at y 100 (in the air, to clear hills). "Quality" is the receiver's `q`: 1.0 is clean, lower means more static, 0 means no carrier.

| Distance (blocks) | Quality |
|---|---|
| 3 | 0.90 |
| 50 | 0.90 |
| 150 | 0.56 (0.83 at y 140) |
| 300 | 0.27 |
| 500 | 0.89 |
| 700 | 0.86 |
| 850 | 0.82 |
| 1000 | 0.76 |
| 1300 | 0.43 |
| 1600 | 0.21 |
| 2000 | 0 (no carrier) |

- Pass: quality falls with distance and the signal is gone by 2,000 blocks. The far points fit the mod's formula, `q = (1 − (d / R)^3.2) × 0.902`, with an effective range R ≈ 1,735 blocks (the transmitter's 862.5 × about 2 from the receiver's antenna and crystal gain).
- **Quirk, worth a look in Phase 1:** the dips at 150 and 300 blocks come from the terrain check (`RadioNetwork.terrain`), which only samples chunks that are loaded. So the same two blocks can get very different reception depending on which chunks happen to be loaded; here the 500-block point read better than the 150- and 300-block points.
- Not checked: hearing the static. These are server-side numbers; the client's static mix was not listened to.
- **Shortcut side effect:** changing the regular transmitter's frequency with `/data merge` knocked it off its Create shaft (`Speed: 0`, "needs power") until the motor was placed again. A redstone standby on and off did not do this, so it's the command, not the mod. Tune powered machines in game, not with `/data merge`.

##### Jammer
Jammer at (38, 70, −16) facing south, about 5 blocks from the regular transmitter–receiver pair (101.0 FM, baseline quality 0.90), with its own creative motor at (37, 70, −16) and a redstone block on top. The code says a running jammer multiplies the quality of any link whose line passes within 48 blocks of it by `0.08 + 0.92 × (d / 48)^1.5`.

| Jammer state | Running | Receiver quality |
|---|---|---|
| Motor only | no | 0.90 |
| Motor + redstone | yes | **0.094** (formula predicts 0.096) |
| Redstone, no motor | no | 0.90 |
| Motor, redstone removed | no | 0.90 |

Pass: it needs both rotation and redstone, and the cut matches the formula.

##### Relay tower
- A Relay Tower (small) placed from its item at (1242, 97, −14), about 1,200 blocks east of the regular transmitter, on a cleared 13×13 pad. Its drive shaft runs along Z; a creative motor at (1242, 97, −16) drives it at 16 RPM. Tuned to 101.0 with `/data merge` *before* the motor went on, to avoid the shaft problem above.
- It reported `active`, `relaying`, **range 3,502 blocks** at 16 RPM (the Ponder scene says about 5,600, presumably at full speed).
- A regular receiver 2,500 blocks east of the transmitter (out of its direct reach): **no carrier** before the relay, **quality 0.93** with the relay running.
- Redstone directly under the core turns the relay off (`active: 0`), and the far receiver loses the station; removing it brings the station back at 0.93. Pass.
- Observation: the relayed signal (0.93) is cleaner than what the relay itself should be receiving at 1,200 blocks (about 0.6 by the sweep above), so a relay seems to pass on a clean copy rather than the static it heard. Possibly by design; worth a look in Phase 1.
- Left in the world: the relay tower and its pad at x ≈ 1,242, plus the regular transmitter, receiver and jammer near the bench. All force-loads were removed afterwards.

##### Screens
Done as the player through s00d's screen tools; screenshots are in `run/screenshots/`.

**Tuning table** (the one at (33, 68, −5)). Its controls are normal widgets, so `screen.state` lists them (AM, FM, the frequency box, six step buttons, `<<`, `>>`, Spec, Reach).
- A frequency card in the slot shows its frequency, a signal-strength bar and a spectrum. The spectrum showed both live stations: 98.1 (creative transmitter) and 101.0 (regular transmitter). Pass.
- `>>` seeks to the next station: 98.10 → 101.00, signal bar 91% (matches the 0.90 quality measured for that transmitter). `<<` seeks back to 98.10. The card keeps the tuning (`airwaves:tuning {freq: 101.0}`). Pass.
- Right-clicking the creative receiver with the 101.0 card retuned it from 98.1 to 101.0 (`airwaves:tune` sound) and it picked up the regular transmitter; the 98.1 card set it back. Pass.
- Oddity: a click on the speaker with the card also played the `tune` sound, but the speaker has no frequency of its own (`SpeakerBE` isn't tunable), so nothing changed. Harmless, maybe misleading.

**Circuit workbench** (placed at (37, 68, −7)). The board grid, the 10 blueprints (Radio, RF, Tuner, Amplifier, Audio, Display, Control, Computer, Sensor, Logic), Auto and Clear all draw. These controls are not reported as widgets, so they were clicked by position (GUI 427×240: Radio blueprint (282, 21), Auto (279, 155)).
- Blank circuit board in its slot, Radio blueprint, Auto: it laid out the 8 parts (coil, 2 capacitors, crystal oscillator, LED, transistor, 2 resistors) and the copper traces, listed the stages (tuned tank, oscillator, transistor amp, indicator) and said "Ready: take your Radio Board". Taking it used up the board and the parts and gave one Radio Board. Pass.
- 36 Airwaves crafting recipes are in the client's recipe book; no errors in the client log. Pass.

Still open: voice through a real microphone (needs you and a headset), the optional second client, and the optional old world.

#### Results so far (2026-11-09, dev client, LAN world in creative)
- The companion "Airwave" was summoned through Numen and driven from opencode. It cannot type commands, so the world was switched to creative by hand.
- It built a test bench just east of spawn (around x 31–33, y 68, z −9 to −6): two antennas on copper towers, a transmitter, a tuning table and a radio. The bench looks right on screen, and the companion reached the tuning table (`goto airwaves:tuning_table` in the log).
- Not confirmed: opencode's wrap-up says the transmitter and tuning table work, but nothing in the log shows a signal or tuned frequency, and no one has listened yet. Treat the checklist items above as still open.
- A later `goto airwaves:creative_transmitter` failed with `NO_PATH` (none in the loaded area), so the bench may use the regular transmitter rather than the creative one.
- The audio items (tape, static over distance, other frequency, relay and jammer) need someone to listen. The companion's tools can't hear sound or fill the tape player slots, so these are by hand.
- opencode ran out of its free model quota partway through, and its todo list is out of date; this section replaces it.

Seen in the log, none blocking:
- Opening to LAN: voice chat failed to bind UDP 53633 (`Address already in use`) and the voice server came up on 53606 instead.
- `powergridNative7` native library not found, so PowerGrid falls back to its non-accelerated solver.
- Electroenergetics' JEI plugin throws `NoClassDefFoundError` (JEI version mismatch) and is missing the `duplex_wire_segment` model; Veil fails to compile one compute shader. All come from dependencies, not Airwaves.

Later, not Phase 0: the companion suggested performance mods for 1.21.1 NeoForge (Embeddium or Sodium, FerriteCore, ModernFix, EntityCulling, ImmediatelyFast, Dynamic FPS; Canary and Spark to measure). Untested with Airwaves.

### 6. Fix and repeat (Claude)
- Fix the bugs you find, rebuild, and send a new jar. Repeat until the checklist passes or what's left is minor.

## Done when
- The mod builds from this repo.
- The checklist passes on your PC, or the remaining issues are written down.

## Questions before starting
1. ~~Bring the reconstruction's code into this repo?~~ Done: the fork already has it.
2. What's already on your PC: Java 21, an old Airwaves world? (Prism Launcher: yes.)
3. How should Claude send each test build? By committing the jar to a `builds/` folder on a branch, or another way you prefer?
