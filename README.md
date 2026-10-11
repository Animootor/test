# Anaconda HTN Observatory

A browser prototype of the "Anaconda predator on Fluid HTN" design plan, built with three.js. You can watch it but not play it. Two anacondas share a small island with four pools, four humans and two small capybara herds: a giant female that ambushes from Reed Pool, and a smaller **tree anaconda** that lives in a grove of big trees to the west and hunts from the branches. Each has its own HTN brain, and a live panel shows what the selected snake's planner is doing.

Both snakes start hungry, so the first ambushes play out within a few minutes. Food is limited (see "Prey is a shared, limited resource" below), so the two snakes compete for the same animals. After a meal it digests for days (a capybara about 6 game days, a person about 8; one game day is 360 s), so hunts are rare events. Use 30× to watch the days go by, or "Make hungry" to start the next hunt.

Open `index.html` in a browser. It loads three.js r128 from jsDelivr, so it needs an internet connection.

## What you see

- **3D view.** Drag to orbit and scroll to zoom. "Follow snake" keeps the camera on the selected snake's head (and lifts it when that snake is up a tree; the grove's crowns fade so you can see it from above).
- **Snake selector.** Tabs at the top of the panel ("Giant female" / "Tree anaconda") choose which brain the panel, the gizmos and the follow camera show. The dot on each tab is that snake's intent colour.
- **The snake.** One continuous body with a flat belly, paired black dorsal ovals, black-ringed orange side spots and a yellow belly. It moves in travelling S-waves (larger when swimming), creeps almost straight with a ripple of muscle when stalking, and holds its head low. Submerged, only the eyes and nostrils break the surface; swimming, only the ridge of the back. Rings spread from the snout as it swims (forming a V-wake) and from the nostrils when it breathes. A kill is held limp on its side in the jaws, wrapped in loops (and held under, with bubbles, when the snake drowns it), then swallowed head-first; the meal shows as a bulge that moves down the body as it is digested.
- **The island.** Gently rolling ground with worn paths from camp to the terminals and pools, muddy shores sloping down into the pool basins, reeds with cattails and lily pads at the margins, sand beaches into the sea, broadleaf trees, birches, spruces, shrubs and rocks. Grass and reeds sway in the wind. Pool water has moving ripples, gets darker and less clear with depth, and reflects the sky at grazing angles, so a submerged snake shows as a dim shape.
- **Day and night.** A sky dome with a sun that turns orange near the horizon, stars and a moon at night, and fog that thickens at night. Nights are dark: moonlight and the flickering campfire are the main light.
- **Prey.** People walk, jog and run (leaning forward) with swinging arms and bending knees, crouch-walk low, kneel to drink at the shore, work at the terminals, and look around (more often after they have seen the snake). Capybaras trot, graze with their heads down (looking up now and then), drink, and swim with only the head and back above water, leaving ripples. Newcomers fade in, and a swallowed animal fades out.
- **The tree anaconda.** Smaller (about 60% of the giant's length, ~21 m against ~36 m) and lighter, with a more olive-yellow pattern. It climbs trunks in a loose spiral with its belly against the bark, travels along thick limbs, reaches across the short gap between neighbouring crowns, lies along a limb with its head hanging down over a trail, and drops onto prey passing beneath.
- **Labels and rings.** A label above each snake's head names the current task, its branch and a short reason (the place, or the target and the sense that found it). A ring under the head shows the intent colour (red hunting, gold feeding, teal digesting, purple fleeing).
- **Gizmos:**
  - Yellow fan: the snake's vision wedge.
  - Red ring: its belief about where the target is. The ring's radius is the position error.
  - Purple rings: vibrations it felt, drawn where it *thinks* they came from.
  - Coloured dots: scent marks (5 m apart, fading over 240 s).
  - Red dashed line: from the head to where the snake believes its target is.
  - Pale outline on the water: where the submerged body lies.
  - Teal ring: the ambush site.
  - Orange line: the current move goal.
  - Large faint ring: the home range.
- **Brain panel** (the task header stays at the top while you scroll; on a phone the controls become one scrolling row and the view takes 60% of the height):
  - Current task and how long it has run.
  - Branch path and MTR.
  - Each executing condition of the current task, marked pass or fail.
  - Plan queue, including paused partial plans.
  - Drives.
  - Per-prey belief confidence and which sense supplied it.
  - All 25 world-state bytes (the plan's 21 plus four rival bytes). The last one that changed is highlighted.
  - The last 32 replans with their reasons.
  - The domain tree. Valid nodes are bright, the current plan path is orange, and the running task is highlighted.
  - The full decomposition log.
- **Click tools:**
  - **Stomp**: makes a strong ground vibration (both snakes can feel it).
  - **Director: rush here**: the Director picks the best eligible snake (alive, not carrying a kill, not mid-strike; of those, the nearest to the click) and fills its root `Slot DIRECTOR` with a `RushTo` sub-domain. The panel switches to that snake.
  - **Poke**: threatens the snake nearest the click (within 20 m; otherwise the selected one). Its stress goes to "threatened", so its Survive branch takes over.
- **Panel extras:** the prey section starts with a belief row for the *other* snake (distance, sense, its published intent), shows island prey counts and when the next newcomer arrives, and marks which snake holds a claim on each prey.
- **Scenario:**
  - Choose the pool snake: a giant female (calm, patient, an almost pure water-ambusher) or a small male (fast, bold, balls up when threatened, forages on land and by day when an ambush runs out of patience). The tree anaconda is always present. Restart resets both.
  - Jump the clock to dawn, day, dusk or night.
  - "Make hungry" sets the selected snake's hunger to starving.

## What is implemented from the plan

| Plan part | Status here |
| --- | --- |
| Part 2: Fluid HTN semantics | A small JS port. It has Select/Sequence/Action/Slot/PausePlan, conditions checked during decomposition and again on dequeue, and executing conditions checked every tick (failure replans in the same tick). It applies PlanOnly, PlanAndExecute and Permanent effects, rolls back a Sequence on failure, and resumes partial plans. A dirty replan must beat the running plan's MTR, otherwise it is rejected (the panel counts rejections). |
| Part 3: senses and beliefs | Sight (20 m by day, 14 m at dawn and dusk, 8 m at night; 200° wedge, tree-trunk line of sight, 2 s grace). Vibration (silent below 0.5 m/s; carries 20/50/100 m at walking/jogging/sprinting speed; 15% position error; under half the range and weaker while submerged). Water ripples: prey wading, drinking or swimming in the snake's own pool are felt up to 28 m with 6% error (reported as vibration). Scent marks (5 m spacing, ring of 64, 240 s; smelt from 11 m and weighted more at night). Per-prey beliefs whose confidence decays faster the longer there is no contact. The target is the best confidence × preference, with a 20% margin before switching. |
| Part 4: drives | Hunger, digestion, body heat (follows the sun, cooler in water and shade, warmer when basking), fatigue, stress. Digestion length scales with the meal (see below); hunger only grows once the meal is mostly digested. |
| Part 5: world state | The plan's 21 bytes plus four rival bytes (25), all set by one quantiser with hysteresis. `InTree` is real (enter above 1.2 m, leave below 0.6 m). Season, Breeding and Shedding stay at 0. The rival bytes are listed below. |
| Part 6: domain | Director slot, Survive, Feed, Hunt (StrikeNow, Pursue, WaitInAmbush, StalkSeen with a Sneak/Creep split, Investigate, AmbushCutOff, FollowTrail, AquaticLieInWait), Digest, Breed, Thermoregulate, Ambient, Idle, in the plan's priority order. Hunting is an aquatic ambush: the snake picks the shore with the most fresh prey scent, swims in along the pool so its body lies in the water, and waits submerged. WaitInAmbush holds still (or glides underwater toward prey at the water's edge) while prey come to its pool, so StrikeNow fires from the water without a telegraph. StalkSeen, Investigate, AmbushCutOff and FollowTrail never pull the snake out of a fresh ambush and only run in its active hours (not by day unless bold). Away from water they also need a bold or starving snake in a short active-foraging spell after an ambush ran out of patience. Pursue is for the small male, or for quarry in or at the water. Kills are dragged at least ~4 m into the pool and held under (Drowning). |
| Part 7: commitment | Lunge has no executing conditions. Tasks with a minimum dwell (Submerge, HoldAmbush, the rest and bask tasks, constriction and swallowing) cannot be cut short by a replan *or* by a failed non-urgent executing condition until the dwell has passed. Only Stress, a Director order, or the task's own break-out (for the ambush: a strike window or prey arriving) can end it sooner. The ambush lasts at least a quarter of the snake's patience. |
| Part 10: Director | RushTo/CampPoint through the slot. Orders clear themselves when they expire, when they finish, or when the snake sees prey. |
| Part 11: ecosystem | Two snakes at once (pool snake + tree anaconda), each with its own Domain instance, context, body and drives. An ecosystem board (§11.2) holds prey claims and each snake's published intent. See "Two snakes" below. |

## Two snakes

### The tree anaconda

Basis: Barrio-Amorós & Manrique (2008) found green anacondas resting in trees up to about 4 m above the ground, probably subadult females or adult males. Everything beyond resting in trees is adapted or invented for this demo.

| Behaviour | How it works | Label |
| --- | --- | --- |
| Resting and digesting in trees | Rests in a crown on an inner limb by day (`RestInCanopy`), digests up a tree (`DigestInTree`), and lies at a tree base just after a big meal (`DigestAtTreeBase`). | REAL (the 2008 observation) / ADAPTED |
| Climbing | Walks to a trunk, then climbs it in a loose spiral (about 4.6 m of height per turn), belly against the bark. | ADAPTED |
| Canopy travel | The six grove trees form a graph. Neighbours up to 12.6 m apart each send a limb toward the other, so a crossing is limb, then a 1.8–3 m reach between limb tips, then limb. Routes are found breadth-first through the graph (`TraverseCanopy`, and `CanopyTravel` at night). | INVENTED |
| Limb ambush | Picks a low limb (about 4.3–6.6 m up) above the trail, game trails or a pool edge. Each limb is scored by fresh prey scent within 6 m of its tip, the trail, a nearby pool edge, and distance from the feared rival. The snake lies along it with its head hanging about 2.4 m down (`LieOnLimb`). | ADAPTED |
| Drop strike | When prey passes within 3.6 m beneath it, the snake falls on it from the limb (`DropFromLimb` → `Lunge`, with no telegraph). The kill is constricted on the ground. It is dragged only into the shallows, and only if water is within 12 m. | INVENTED |
| Thermoregulation | Basks on a high, sun-facing limb when cold (`BaskOnLimb`) and moves into crown shade when hot (`ShadeInCrown`). | REAL / ADAPTED |
| Defence | Climbs a tree rather than fleeing into water (`ClimbToSafety`), freezes in the canopy (`FreezeInCanopy`), and balls up only when cornered on the ground. | ADAPTED |
| Water | A poor, reluctant swimmer. It steers around pools, swims at 0.45× speed, never ambushes from water and never runs `Submerge`. | INVENTED for this character |
| Senses | The same three senses. From a tree it sees 30% further, sees all round below it (140° half-angle) over trunks, and feels ground vibration at ×0.4. | ADAPTED |

### Interactions (plan §11.3 plus the user's additions)

| Interaction | How it works | Label |
| --- | --- | --- |
| Overlapping home ranges, nothing defended | The giant's range is 100 m around Reed Pool. The tree anaconda's is 60 m around the grove, which reaches the edge of Reed Pool. A starving snake ranges 1.5× wider. | REAL |
| Spacing | About 10 m apart while moving. The smaller snake keeps about 20 m from the giant on the ground (steering away from the giant's body). There is no avoidance while resting, and a digesting snake ignores the other. | ADAPTED |
| Fear of the giant | The tree anaconda's belief about the giant (sight, vibration, scent, measured to the nearest part of the giant's ~35 m body) is quantised into `RivalNear`. *Near* (<20 m): it will not come down, and an ambush limb within 16 m of the giant is abandoned (urgent executing condition, then replan). *Close* (<12 m): Survive takes over. In a tree it freezes; on the ground it climbs the nearest tree away from the giant; cornered, it balls up. Giant females can eat much smaller conspecifics (Rivas & Owens 2000). | REAL basis / ADAPTED |
| Claims (ecosystem board) | At most 2 claimers per prey, and the first claimant keeps it. A claim is staked only on prey the snake is working (confidence ≥ 0.4, within 16 m). `Claim` = 2 when the other snake holds it, so `StrikeNow` is blocked and target scoring halves that prey. | ADAPTED (plan §11.2) |
| Published intent | Every task's Permanent `Intent` effect is also written to the board. The other snake reads it as `RivalIntent`. | ADAPTED (plan §11.2) |
| Kleptoparasitism | A hungry giant that senses the smaller snake holding a kill on the ground (`RivalKill`) runs `Feed › TakeOverKill`. When the giant comes close, the small snake's `Survive › AbandonKill` drops the kill (dead after ~12 s in the coils, otherwise it escapes) and climbs away. The giant then sees `Carcass` = free and runs `Feed › Scavenge`. | INVENTED (not documented for anacondas) |
| Never contests the giant's kill | The small snake has no TakeOverKill (it needs a smaller rival). It scavenges a carcass only when the giant is more than 20 m from it, for example after a Poke-induced regurgitation (the giant leaves a dead kill, or the regurgitated meal, as a carcass). | ADAPTED |
| Scavenging | Any hungry snake may take a free carcass it can see or smell (25 m). A carcass rots away after one game day. | REAL (anacondas take carrion) / ADAPTED |
| Cannibalism | `Hunt › StrikeRival`: a starving giant, with the smaller snake on the ground within strike range. A 30% roll at most every 120 s, then a 50% chance to hold on. If seized, the tree anaconda is constricted and swallowed, and a new one moves into the grove 3 game days later. Very rare in practice, because the small snake keeps away. | REAL basis (Rivas & Owens 2000), rare by design |
| Mating | Not implemented (Breed stays a stub). | — |

### Prey is a shared, limited resource

Four humans and four capybaras (two herds). An eaten animal is replaced only after 3–5 game days (a newcomer from the island edge, away from the snakes). Both snakes hunt the same prey base, so after a few meals the island runs short, hunger climbs to starving, and the snakes widen their ranges and hunt on land. Prey watch for both snakes: awareness builds from whichever snake is most visible. Prey rarely notice a snake that lies still in a tree.

### New domain tasks and world-state bytes

- **Survive:** `AbandonKill` (Escape: `UpATree` / `FleeOverland`), `RegurgitateAndWithdraw` (now `WithdrawToWater` / `ClimbToSafety` / `FleeOverland`), `FreezeInCanopy`, `ClimbToSafety` (PlanOnly `InTree=1` then `FreezeInCanopy`), `Ball`, `StrikeBack`, `FleeOverland`, `FleeToWater`. Survive is valid when stressed, *or* when a feared rival is close.
- **Feed** is now a selector: `EatKill` (ReachWater › Kill › Swallow), `Scavenge` (GoToCarcass › TakeCarcass), `TakeOverKill` (ApproachRivalKill › PausePlan).
- **Hunt:** `StrikeNow` (Ready: BurstFromWater / `DropFromLimb` / Telegraph, then Lunge), `StrikeRival`, Pursue, WaitInAmbush, StalkSeen, Investigate, AmbushCutOff, FollowTrail (land branches also need "safe to come down"), `TreeAmbush` (`LieOnLimb`, or `RelocateToLimb`: ChooseAmbushLimb › GetIntoCanopy [AlreadyInTree / ClimbTrunk with PlanOnly `InTree=1`] › TraverseCanopy › LieOnLimb), and AquaticLieInWait (giant and male only).
- **Digest:** `DigestAtTreeBase`, `DigestInTree`, then the water-side branches (not for the tree anaconda).
- **Thermoregulate:** `BaskOnLimb`, `ShadeInCrown`, then the ground and water branches.
- **Ambient:** `ClimbHome`, `CanopyTravel`, and `RestInCanopy` in DayLieUp.
- **New bytes:** `RivalNear` (none / sensed / near <20 m / close <12 m, with distance and confidence hysteresis and a 6 s hold), `RivalIntent` (from the board), `RivalKill` (the rival holds a kill on the ground within 35 m and is sensed), `Carcass` (a known carcass that is free). A change of `RivalNear` for a feared rival may cut short a committed task's dwell, and "feared rival not close" is an urgent executing condition on the limb tasks.

## Deviations from the plan

- **Hunt condition uses `Digesting < 2`, not `Digesting = 0`.** The plan's §6.5 says Hunt becomes valid again at "sluggish" (1), but §6.2 gives the condition as `= 0`. I followed §6.5. Hunt operators move at 0.6× speed while digesting.
- **Hunt also requires `fatigue < 0.85`, and StrikeNow requires fewer than 3 strikes.** This is the plan's fatigue "risk/energy budget" made concrete. Without it, a snake that keeps missing would keep striking forever.
- **AmbushCutOff waits 30 s after a failed cut-off.** This stops it from re-planning the same impossible intercept over and over.
- **Prey awareness builds up over time** instead of noticing the snake instantly. It builds slower when the snake is moving slowly or is in grass. Prey react to a lunge after about 0.25 s. Without these, prey always escaped. A submerged snake cannot be seen. Some strikes miss on purpose: alert prey, or prey struck in the open, sometimes leap aside.
- **Prey remember danger.** Places where a snake was seen or struck go on a fear map for 4 minutes, and prey avoid them. Humans who have seen the snake become vigilant, warn people nearby, and avoid the pools for a while. When a person is caught, the others run to camp and regroup there. Eaten prey come back after 3–5 game days, from the island edge on the far side from the snakes.
- **Capybaras live in herds.** Each herd grazes slowly around one anchor point (about 0.3 m/s), with its members 2–4 m apart. It drinks at the shore at dawn and dusk and wallows in the water at midday. When one capybara takes alarm, the whole herd flees together, into the nearest pool away from the snake. If the snake is in that pool, they flee onto land instead. A herd whose shore is all feared moves to another pool. Capybaras swim; humans do not.
- **Meals last days.** A capybara takes about 6 game days to digest and a person about 8. The small male digests 1.5× faster. The snake starts hungry so the first hunt happens early.
- **The Breed, RefugeDuringShed and DrySeasonCluster branches are in the tree but never run.** Mating is deliberately not implemented, and there is no shed cycle or dry-season phase.
- **Only the tree anaconda climbs.** The small male's `RestInTree` still rests at the base of the trunk. The tree anaconda climbs only the six big grove trees (the other trees are too thin to carry it).
- **The tree anaconda is much bigger than a real arboreal anaconda** (about 21 m), because everything here is scaled to the ~36 m giant. Its constriction and swallowing take longer than the giant's (50 s and 25 s against 30 s and 4 s), which also gives a rival time to notice the kill.
- **Two snakes, one Director.** Plan §10 has the Director pick the best eligible snake. Here that means the nearest one that is alive, not carrying a kill, and not mid-strike.
- **Capybaras are included as non-player prey,** as raised in open question 11.
- **Heavy kills far from water are eaten on the spot.** If the nearest water is more than 40 m away, the snake swallows there instead of dragging.
- **Time is compressed:** one day is 360 game seconds. Speed buttons (1×, 3×, 10×, 30×) multiply that.
