# Anaconda HTN Observatory

A browser prototype of the "Anaconda predator on Fluid HTN" design plan, built with three.js. You can watch it but not play it. One anaconda lives on a small island with four pools, four humans and four capybaras, and a live panel shows what its planner is doing.

Open `index.html` in a browser. It loads three.js r128 from jsDelivr, so it needs an internet connection.

## What you see

- **3D view.** Drag to orbit and scroll to zoom. "Follow snake" keeps the camera on the head.
- **Gizmos:**
  - Yellow fan: the snake's vision wedge.
  - Red ring: its belief about where the target is. The ring's radius is the position error.
  - Purple rings: vibrations it felt, drawn where it *thinks* they came from.
  - Coloured dots: scent marks (5 m apart, fading over 240 s).
  - Teal ring: the ambush site.
  - Orange line: the current move goal.
  - Large faint ring: the home range.
- **Brain panel:**
  - Current task and how long it has run.
  - Branch path and MTR.
  - Each executing condition of the current task, marked pass or fail.
  - Plan queue, including paused partial plans.
  - Drives.
  - Per-prey belief confidence and which sense supplied it.
  - All 21 world-state bytes. The last one that changed is highlighted.
  - The last 32 replans with their reasons.
  - The domain tree. Valid nodes are bright, the current plan path is orange, and the running task is highlighted.
  - The full decomposition log.
- **Click tools:**
  - **Stomp**: makes a strong ground vibration.
  - **Director: rush here**: fills the root `Slot DIRECTOR` with a `RushTo` sub-domain.
  - **Poke**: sets stress to "threatened", so the Survive branch takes over.
- **Scenario:**
  - Choose a giant female (calm, patient) or a small male (fast, bold, balls up when threatened).
  - Jump the clock to dawn, day, dusk or night.
  - "Make hungry" sets hunger to starving.

## What is implemented from the plan

| Plan part | Status here |
| --- | --- |
| Part 2: Fluid HTN semantics | A small JS port. It has Select/Sequence/Action/Slot/PausePlan, conditions checked during decomposition and again on dequeue, and executing conditions checked every tick (failure replans in the same tick). It applies PlanOnly, PlanAndExecute and Permanent effects, rolls back a Sequence on failure, and resumes partial plans. A dirty replan must beat the running plan's MTR, otherwise it is rejected (the panel counts rejections). |
| Part 3: senses and beliefs | Sight (20 m, 200° wedge, tree-trunk line of sight, 2 s grace). Vibration (silent below 0.5 m/s; carries 20/50/100 m at walking/jogging/sprinting speed; 15% position error). Scent marks (5 m spacing, ring of 64, 240 s). Per-prey beliefs whose confidence decays faster the longer there is no contact. The target is the best confidence × preference, with a 20% margin before switching. |
| Part 4: drives | Hunger, digestion, body heat (follows the sun, cooler in water and shade, warmer when basking), fatigue, stress. |
| Part 5: world state | All 21 bytes, set by one quantiser with hysteresis. Season, Breeding and Shedding stay at 0. |
| Part 6: domain | Director slot, Survive, Feed, Hunt (StrikeNow, Pursue, StalkSeen with a Sneak/Creep split, Investigate, AmbushCutOff, FollowTrail, AquaticLieInWait), Digest, Breed, Thermoregulate, Ambient, Idle, in the plan's priority order. |
| Part 7: commitment | Lunge and Swallow have no executing conditions. Submerge and the rest tasks have a 5 s minimum dwell that only Stress or a Director order can cut short. |
| Part 10: Director | RushTo/CampPoint through the slot. Orders clear themselves when they expire, when they finish, or when the snake sees prey. |
| Part 11: ecosystem | Two personality presets. Only one snake at a time, so there is no ecosystem board. |

## Deviations from the plan

- **Hunt condition uses `Digesting < 2`, not `Digesting = 0`.** The plan's §6.5 says Hunt becomes valid again at "sluggish" (1), but §6.2 gives the condition as `= 0`. I followed §6.5. Hunt operators move at 0.6× speed while digesting.
- **Hunt also requires `fatigue < 0.85`, and StrikeNow requires fewer than 3 strikes.** This is the plan's fatigue "risk/energy budget" made concrete. Without it, a snake that keeps missing would keep striking forever.
- **AmbushCutOff waits 30 s after a failed cut-off.** This stops it from re-planning the same impossible intercept over and over.
- **Prey awareness builds up over time** instead of noticing the snake instantly. It builds slower when the snake is moving slowly or is in grass. Prey react to a lunge after about 0.25 s. Without these, prey always escaped. A submerged snake cannot be seen.
- **The Breed, RefugeDuringShed and DrySeasonCluster branches are in the tree but never run.** They need a second snake, a shed cycle and a dry-season phase, none of which this demo has.
- **No tree climbing.** A climber's `RestInTree` rests at the base of the trunk.
- **Capybaras are included as non-player prey,** as raised in open question 11.
- **Time is compressed:** one day is 360 game seconds. Speed buttons multiply that.
