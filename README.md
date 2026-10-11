# Anaconda HTN Observatory

A browser prototype of the "Anaconda predator on Fluid HTN" design plan, built with three.js. You can watch it but not play it. One anaconda lives on a small island with four pools, four humans and two small capybara herds, and a live panel shows what its planner is doing.

The snake starts hungry, so the first ambush plays out within a few minutes. After a meal it digests for days (a capybara about 6 game days, a person about 8; one game day is 360 s), so hunts are rare events. Use 30× to watch the days go by, or "Make hungry" to start the next hunt.

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
  - Choose a giant female (calm, patient, an almost pure water-ambusher) or a small male (fast, bold, balls up when threatened, forages on land and by day when an ambush runs out of patience).
  - Jump the clock to dawn, day, dusk or night.
  - "Make hungry" sets hunger to starving.

## What is implemented from the plan

| Plan part | Status here |
| --- | --- |
| Part 2: Fluid HTN semantics | A small JS port. It has Select/Sequence/Action/Slot/PausePlan, conditions checked during decomposition and again on dequeue, and executing conditions checked every tick (failure replans in the same tick). It applies PlanOnly, PlanAndExecute and Permanent effects, rolls back a Sequence on failure, and resumes partial plans. A dirty replan must beat the running plan's MTR, otherwise it is rejected (the panel counts rejections). |
| Part 3: senses and beliefs | Sight (20 m by day, 14 m at dawn and dusk, 8 m at night; 200° wedge, tree-trunk line of sight, 2 s grace). Vibration (silent below 0.5 m/s; carries 20/50/100 m at walking/jogging/sprinting speed; 15% position error; under half the range and weaker while submerged). Water ripples: prey wading, drinking or swimming in the snake's own pool are felt up to 28 m with 6% error (reported as vibration). Scent marks (5 m spacing, ring of 64, 240 s; smelt from 11 m and weighted more at night). Per-prey beliefs whose confidence decays faster the longer there is no contact. The target is the best confidence × preference, with a 20% margin before switching. |
| Part 4: drives | Hunger, digestion, body heat (follows the sun, cooler in water and shade, warmer when basking), fatigue, stress. Digestion length scales with the meal (see below); hunger only grows once the meal is mostly digested. |
| Part 5: world state | All 21 bytes, set by one quantiser with hysteresis. Season, Breeding and Shedding stay at 0. |
| Part 6: domain | Director slot, Survive, Feed, Hunt (StrikeNow, Pursue, WaitInAmbush, StalkSeen with a Sneak/Creep split, Investigate, AmbushCutOff, FollowTrail, AquaticLieInWait), Digest, Breed, Thermoregulate, Ambient, Idle, in the plan's priority order. Hunting is an aquatic ambush: the snake picks the shore with the most fresh prey scent, swims in along the pool so its body lies in the water, and waits submerged. WaitInAmbush holds still (or glides underwater toward prey at the water's edge) while prey come to its pool, so StrikeNow fires from the water without a telegraph. StalkSeen, Investigate, AmbushCutOff and FollowTrail never pull the snake out of a fresh ambush and only run in its active hours (not by day unless bold). Away from water they also need a bold or starving snake in a short active-foraging spell after an ambush ran out of patience. Pursue is for the small male, or for quarry in or at the water. Kills are dragged at least ~4 m into the pool and held under (Drowning). |
| Part 7: commitment | Lunge has no executing conditions. Tasks with a minimum dwell (Submerge, HoldAmbush, the rest and bask tasks, constriction and swallowing) cannot be cut short by a replan *or* by a failed non-urgent executing condition until the dwell has passed. Only Stress, a Director order, or the task's own break-out (for the ambush: a strike window or prey arriving) can end it sooner. The ambush lasts at least a quarter of the snake's patience. |
| Part 10: Director | RushTo/CampPoint through the slot. Orders clear themselves when they expire, when they finish, or when the snake sees prey. |
| Part 11: ecosystem | Two personality presets. Only one snake at a time, so there is no ecosystem board. |

## Deviations from the plan

- **Hunt condition uses `Digesting < 2`, not `Digesting = 0`.** The plan's §6.5 says Hunt becomes valid again at "sluggish" (1), but §6.2 gives the condition as `= 0`. I followed §6.5. Hunt operators move at 0.6× speed while digesting.
- **Hunt also requires `fatigue < 0.85`, and StrikeNow requires fewer than 3 strikes.** This is the plan's fatigue "risk/energy budget" made concrete. Without it, a snake that keeps missing would keep striking forever.
- **AmbushCutOff waits 30 s after a failed cut-off.** This stops it from re-planning the same impossible intercept over and over.
- **Prey awareness builds up over time** instead of noticing the snake instantly. It builds slower when the snake is moving slowly or is in grass. Prey react to a lunge after about 0.25 s. Without these, prey always escaped. A submerged snake cannot be seen. Some strikes miss on purpose: alert prey, or prey struck in the open, sometimes leap aside.
- **Prey remember danger.** Places where the snake was seen or struck go on a fear map for 4 minutes, and prey avoid them. Humans who have seen the snake become vigilant, warn people nearby, and avoid the pools for a while. When a person is caught, the others run to camp and regroup there. Eaten prey come back from the island edge on the far side from the snake.
- **Capybaras live in herds.** Each herd grazes slowly around one anchor point (about 0.3 m/s), with its members 2–4 m apart. It drinks at the shore at dawn and dusk and wallows in the water at midday. When one capybara takes alarm, the whole herd flees together, into the nearest pool away from the snake. If the snake is in that pool, they flee onto land instead. A herd whose shore is all feared moves to another pool. Capybaras swim; humans do not.
- **Meals last days.** A capybara takes about 6 game days to digest and a person about 8. The small male digests 1.5× faster. The snake starts hungry so the first hunt happens early.
- **The Breed, RefugeDuringShed and DrySeasonCluster branches are in the tree but never run.** They need a second snake, a shed cycle and a dry-season phase, none of which this demo has.
- **No tree climbing.** A climber's `RestInTree` rests at the base of the trunk.
- **Capybaras are included as non-player prey,** as raised in open question 11.
- **Heavy kills far from water are eaten on the spot.** If the nearest water is more than 40 m away, the snake swallows there instead of dragging.
- **Time is compressed:** one day is 360 game seconds. Speed buttons (1×, 3×, 10×, 30×) multiply that.
