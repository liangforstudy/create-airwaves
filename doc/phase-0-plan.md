# Phase 0 — Get it running

Status: **steps 1–4 done. The dev client runs; the field manual, Ponder and creative tabs were checked on 2026-11-09. Numen 0.1.2 is wired into the dev run and opencode drives a companion in game, and it built a test bench near spawn. Next: the audio half of the checklist (step 5), by ear.**

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
- [ ] Tape player → cable → transmitter → radio on the same frequency: you hear the tape.
- [ ] Moving the radio farther away adds more static.
- [ ] A radio on a different frequency stays silent.
- [ ] Relay and jammer behave sensibly.
- [ ] Circuit workbench opens, and crafting recipes appear.
- [ ] Optional: a second client hears the transmission.
- [ ] Optional: an old world from the original mod still loads.

For anything broken, a sentence or a screenshot is enough. The game log helps if there's a crash.

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
