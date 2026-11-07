# Phase 0 — Get it running

Status: **plan, waiting for the user's go-ahead.** Nothing downloaded or built yet.

Goal: get the reconstructed mod ([vigasilva/Create-Airwaves](https://github.com/vigasilva/Create-Airwaves), branch `master`) to build, and confirm the main features work in a game. No new features and no cleanup in this phase. That's Phase 1.

Note: Liangforstudy has already folked: https://github.com/liangforstudy/create-airwaves-1

## Who does what
- **Claude (in the cloud):** downloads the code, builds it and fixes build errors. Can't open Minecraft.
- **You (on your PC):** play-test the mod in Minecraft and say what works and what doesn't.

## Steps

### 1. Bring in the code (Claude)
- Copy the reconstruction's code into this repo, keeping its history so credit to its author stays visible.
- Add a short README note: unofficial revival, original authors credited, CC BY-NC-SA 4.0. The full credits come in Phase 1.

### 2. Build it (Claude)
- The cloud machine already has Java 21.
- Run `./gradlew build`. The first run downloads Minecraft, NeoForge, Create and the other parts it needs, which takes a while.
- If it fails, fix only what stops the build, and note each fix.
- Result: a mod file in `build/libs/`, e.g. `airwaves-1.21.1-0.1.1.jar`.

### 3. Quick automatic check (Claude, optional)
- Try starting a headless test server (`./gradlew runServer`) to see whether the mod loads without crashing.
- This needs Create, Aeronautics and Sable to be present. If that's awkward in the cloud, skip it and rely on step 5.

### 4. Set up your PC (you, with simple steps from Claude)
- Install Java 21 and Prism Launcher, if you don't already have them.
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
1. Is it okay to bring the reconstruction's code into this repo, keeping its history?
2. What's already on your PC: Java 21, Prism Launcher, an old Airwaves world?
3. How should Claude send each test build? By committing the jar to a `builds/` folder on a branch, or another way you prefer?
