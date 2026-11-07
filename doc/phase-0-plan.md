# Phase 0 — Get it running

Status: **plan, waiting for the user's go-ahead.** Step 1 is done (the code is in the fork). Nothing built yet.

Goal: get the reconstructed mod (the fork [liangforstudy/create-airwaves-1](https://github.com/liangforstudy/create-airwaves-1), branch `master`, forked from [vigasilva/Create-Airwaves](https://github.com/vigasilva/Create-Airwaves)) to build, and confirm the main features work in a game. No new features and no cleanup in this phase. That's Phase 1.

## Who does what
- **Claude (in the cloud):** downloads the code, builds it and fixes build errors. Can't open Minecraft.
- **You (on your PC):** play-test the mod in Minecraft and say what works and what doesn't.

## Steps

### 1. Bring in the code ✅ done
- The user forked vigasilva/Create-Airwaves as liangforstudy/create-airwaves-1, so the code and its history are already here.
- The existing README already says it's unofficial and keeps CC BY-NC-SA 4.0. A note about this revival and the full credits come in Phase 1.

### 2. Build it (Claude)
- The cloud machine already has Java 21.
- Run `./gradlew build`. The first run downloads Minecraft, NeoForge, Create and the other parts it needs, which takes a while.
- If it fails, fix only what stops the build, and note each fix.
- Result: a mod file in `build/libs/`, e.g. `airwaves-1.21.1-0.1.1.jar`.

### 3. Quick automatic check (Claude, optional)
- Try starting a headless test server (`./gradlew runServer`) to see whether the mod loads without crashing.
- This needs Create, Aeronautics and Sable to be present. If that's awkward in the cloud, skip it and rely on step 5.

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
