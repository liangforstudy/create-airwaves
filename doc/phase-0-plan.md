# Phase 0 — Get it running

Status: **step 2 done (builds), step 3 done (dev server loads). Waiting on the play-test in step 5.**

Goal: get the reconstructed mod (the fork [liangforstudy/create-airwaves](https://github.com/liangforstudy/create-airwaves), branch `master`, forked from [vigasilva/Create-Airwaves](https://github.com/vigasilva/Create-Airwaves)) to build, and confirm the main features work in a game. No new features and no cleanup in this phase. That's Phase 1.

## Who does what
- **Claude (in the cloud):** downloads the code, builds it and fixes build errors. Can't open Minecraft.
- **You (on your PC):** play-test the mod in Minecraft and say what works and what doesn't.

## Steps

### 1. Bring in the code ✅ done
- The user forked vigasilva/Create-Airwaves as liangforstudy/create-airwaves, so the code and its history are already here.
- The existing README already says it's unofficial and keeps CC BY-NC-SA 4.0. A note about this revival and the full credits come in Phase 1.

### 2. Build it (Claude) ✅ done
- The cloud machine already has Java 21.
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

### 4. Set up your PC (you, with simple steps from Claude)
- Install Java 21, if you don't already have it. (Prism Launcher is already installed.)
- Make a Minecraft 1.21.1 NeoForge instance in Prism.
- Add the mods to it:
  - Create 6.0.10 or newer (below 6.1)
  - Create: Aeronautics
  - Sable
  - the Airwaves jar from step 2
- Optional extras: JEI, Simple Voice Chat, Create: Power Grid.

### 5. Play-test checklist (you)
Use a creative test world. No microphone is needed for this.
- [ ] The game starts, and the Airwaves items show in the creative tab or JEI.
- [ ] Tape player → cable → transmitter → radio on the same frequency: you hear the tape.
- [ ] Moving the radio farther away adds more static.
- [ ] A radio on a different frequency stays silent.
- [ ] Relay and jammer behave sensibly.
- [ ] Circuit workbench opens, and crafting recipes appear.
- [ ] Ponder scenes open (hold W over an item).
- [ ] The field manual opens.
- [ ] Optional: a second client hears the transmission.
- [ ] Optional: an old world from the original mod still loads.

For anything broken, a sentence or a screenshot is enough. The game log helps if there's a crash.

### 6. Fix and repeat (Claude)
- Fix the bugs you find, rebuild, and send a new jar. Repeat until the checklist passes or what's left is minor.

## Done when
- The mod builds from this repo.
- The checklist passes on your PC, or the remaining issues are written down.

## Questions before starting
1. ~~Bring the reconstruction's code into this repo?~~ Done: the fork already has it.
2. What's already on your PC: Java 21, an old Airwaves world? (Prism Launcher: yes.)
3. How should Claude send each test build? By committing the jar to a `builds/` folder on a branch, or another way you prefer?
