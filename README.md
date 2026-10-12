# Anaconda HTN Observatory

A browser prototype of the "Anaconda predator on Fluid HTN" design plan, built with three.js. You can watch it but not play it. Two anacondas share a small island with four pools, four humans and two small capybara herds: a giant female that ambushes from Reed Pool, and a smaller **tree anaconda** that lives in a grove of big trees to the west and hunts from the branches. Each has its own HTN brain, and a live panel shows what the selected snake's planner is doing.

Both snakes start hungry, so the first ambushes play out within the first game day. Food is limited (see "Prey is a shared, limited resource" below), so the two snakes compete for the same animals. After a meal it digests for days (an adult capybara about 6 game days, a young one 2–3, a person about 8; one game day is 360 s), so hunts are rare events, and many of them fail. Weather, the animals' own memories and their body state make every day a little different (see "Days that differ" below). Use 30× to watch the days go by, or "Make hungry" to start the next hunt.

Open `index.html` in a browser. It loads three.js r128 from jsDelivr, so it needs an internet connection. All shaders are compiled before the first frame (a short "Building the island…" screen covers the build), and the camera starts framed on the pool snake.

## What you see

- **3D view.** Drag to orbit and scroll to zoom. "Follow snake" keeps the camera on the selected snake's head (and lifts it when that snake is up a tree; the grove's crowns fade so you can see it from above).
- **Snake selector.** Tabs at the top of the panel ("Giant female" / "Tree anaconda") choose which brain the panel, the gizmos and the follow camera show. The dot on each tab is that snake's intent colour.
- **The snake.** One continuous body with a flat belly. The pattern (paired round black dorsal spots, black-ringed orange side ocelli, black blotches on a yellow belly, a fine scale grain and a slight ring-to-ring colour variation) is baked into a texture laid out by arc length along the body, so it does not smear on bends. It moves in travelling S-waves (larger when swimming), creeps almost straight with a ripple of muscle when stalking, and holds its head low when prowling. The head is matte and darker on top, with the eyes sunk into their sockets under a brow ridge. Submerged, only the eyes and nostrils break the surface, with a bright meniscus where the head cuts the water; swimming, only the ridge of the back shows; floating, the whole back lies at the surface; when it *sinks low* (disturbed) the eyes go under too. Before a strike on land the neck draws back into an S (no gape). Rings spread from the snout as it swims (a V-wake) and from the nostrils when it breathes. Parts of the body that would overlap are pushed apart (only a balled-up snake and the constriction coils stack on purpose), and the two snakes never pass through each other.
- **Kills.** A kill is held limp on its side in the jaws, then wrapped in loops with its head and shoulders sticking out. In water the coils first lie at the surface with the prey thrashing among them (splashes, spray, rings, squeeze pulses), then sink as the struggle fades and the prey is held under (bubbles). It is swallowed head-first while the loops unwind into a flat coil; the meal shows as a bulge that moves down the body as it is digested. After a drop from a limb, the coils form on the ground while the rest of the body is pulled down off the limb after the head.
- **The island.** Gently rolling ground with worn paths from camp to the terminals and pools, pools with irregular, organic shorelines (the simulation uses the same outline for every shore and depth query), muddy shores sloping down into the basins, reeds with cattails and lily pads at the margins, sand beaches into the sea, broadleaf trees, birches, spruces, shrubs and rocks. The six grove trees are big, kapok-like trees: plank buttresses at the foot, ridged grey-brown bark with moss low on the shady side, thick limbs with swollen collars, leafy sprays over the limb tips and a high, flat-topped crown. Grass and reeds sway in the wind and are pressed down and pushed aside by both snakes' bodies. Pool water has moving ripples, gets darker and less clear with depth, and reflects the sky at grazing angles, so a submerged snake shows as a dim shape.
- **Day and night, and weather.** A sky dome with a sun that turns orange near the horizon, stars and a moon at night, and fog that thickens at night. Nights are dark: moonlight and the flickering campfire are the main light. Dawn and dusk have a warm, low sun and a brighter fill light. The sky drifts between clear, overcast and rain over hours (shown next to the clock): a drifting cloud layer thickens with the cloud cover, overcast greys the sky and dims the sun, and rain adds falling streaks, rain rings on the water and thicker fog. At night the moon glints on the water only at grazing angles.
- **Prey.** People walk, jog and run (leaning forward) with swinging arms and bending knees, crouch-walk low, kneel to drink at the shore, stroll along a shore, work at the terminals, and look around (more often after they have seen the snake). Capybaras trot, graze with their heads down (looking up now and then), drink, and swim with only the head and back above water, leaving ripples. Young capybaras are smaller. Newcomers fade in, and a swallowed animal fades out.
- **Small movements.** The tongue flicks in bursts of 2–6, often when the snake is investigating or picks up a new scent or tremor, now and then while travelling, and rarely when it is settled. The head turns toward whatever it senses; while looking around it makes short looks and holds them; at rest it lies still with a rare small shift.
- **The tree anaconda.** Smaller (about 60% of the giant's length, ~21 m against ~36 m) and lighter, with a more olive-yellow pattern. It climbs trunks in a loose spiral with its belly against the bark, travels along thick limbs, reaches across the short gap between neighbouring crowns, lies along a limb with its head hanging down over a trail, and drops onto prey passing beneath.
- **Labels and rings.** A label above each snake's head names the current task, its branch and a short reason (the place, or the target and the sense that found it); on a phone it floats a little higher on a leader line so it does not cover the snake. A thin ring under the head (around the coils while it feeds) shows the intent colour (red hunting, gold feeding, teal digesting, purple fleeing); it is faint while the snake feeds and when the gizmos are off.
- **Gizmos:**
  - Yellow fan: the snake's vision wedge (faint, fading with distance).
  - Red ring: its belief about where the target is. The ring's radius is the position error. Hidden once the target is within strike range.
  - Purple rings: vibrations it felt, drawn where it *thinks* they came from (at most the three newest per animal).
  - While the snake feeds, the vision fan, scent dots and lines are toned down and the belief ring, move goal and ambush ring are hidden.
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
  - Drives (hunger, digestion, body heat, fatigue, stress, body condition).
  - Per-prey belief confidence and which sense supplied it.
  - All 28 world-state bytes (the plan's 21, four rival bytes, and `Sky`, `RestPref`, `HuntMood`). The last one that changed is highlighted.
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
| Part 4: drives | Hunger, digestion, body heat, fatigue, stress, and body condition (reserves). Body heat relaxes toward the air (insolation and the day's temperature), the water (buffered), or shade; basking adds heat in proportion to insolation, more slowly for a bigger body. Digestion and the return of hunger run faster when the snake is warm. Digestion length scales with the individual prey's mass; hunger only grows once the meal is mostly digested, and returns more slowly when the snake is in good condition. Stress comes from people (graded, see below), Poke, or a feared rival. |
| Part 5: world state | The plan's 21 bytes, four rival bytes, and three added bytes (28), all set by one quantiser with hysteresis. `InTree` is real (enter above 1.2 m, leave below 0.6 m). Season, Breeding and Shedding stay at 0. The rival bytes and the added bytes are listed below. |
| Part 6: domain | Director slot, Survive, Feed, Hunt, Digest, Breed, Thermoregulate, Ambient, Idle, in the plan's priority order. Hunting is an aquatic ambush: the snake picks a shore spot from fresh prey scent and its memory of which pools have had traffic, swims in so its body lies in the water, and waits submerged. WaitInAmbush holds still (or glides underwater toward prey at the water's edge) while prey come to its pool, so StrikeNow fires from the water. There is no open-mouth display before a predatory strike (Telegraph is only used in Survive › StrikeBack). StalkSeen, Investigate, AmbushCutOff and FollowTrail never pull the snake out of a fresh ambush and only run in its active hours (not by day unless bold). Away from water they also need a bold or starving snake in a short active-foraging spell, within ~25 m of water unless starving. Pursue is only for the small male, or for quarry in the water. Kills are dragged at least ~4 m into the pool (from up to 80 m inland) and held under (Drowning). The full list of branches is under "Days that differ" below. |
| Part 7: commitment | Lunge has no executing conditions. Tasks with a minimum dwell (Submerge, HoldAmbush, the rest and bask tasks, constriction and swallowing) cannot be cut short by a replan *or* by a failed non-urgent executing condition until the dwell has passed. Only Stress, a Director order, or the task's own break-out (for the ambush: a strike window or prey arriving) can end it sooner. The ambush lasts at least a quarter of the snake's patience. |
| Part 10: Director | RushTo/CampPoint through the slot. Orders clear themselves when they expire, when they finish, or when the snake sees prey. |
| Part 11: ecosystem | Two snakes at once (pool snake + tree anaconda), each with its own Domain instance, context, body and drives. An ecosystem board (§11.2) holds prey claims and each snake's published intent. See "Two snakes" below. |

## Two snakes

### The tree anaconda

Basis: Barrio-Amorós & Manrique (2008) found green anacondas resting in trees up to about 4 m above the ground, probably subadult females or adult males. Everything beyond resting in trees is adapted or invented for this demo.

| Behaviour | How it works | Label |
| --- | --- | --- |
| Resting and digesting in trees | Rests in a crown by day (`RestInCanopy`), lies at a tree base just after a big meal (`DigestAtTreeBase`), then digests up a tree. How it lies follows `RestPref`: on a sunny high limb (`BaskOnLimb`), stretched along a limb in dappled light (`LieAlongLimb`), tucked into a fork near the trunk (`RestInFork`), or in crown shade (`ShadeInCrown`). The perch is chosen among the limbs of this tree and its neighbours (any grove tree at night), at varied points along each limb, avoiding perches used in the last few days. It may stay on its limb and just lie differently. A light digester may move to another crown at night. | REAL (the 2008 observation) / ADAPTED |
| Climbing | Walks to a trunk, then climbs it in a loose spiral (about 4.6 m of height per turn), belly against the bark. | ADAPTED |
| Canopy travel | The six grove trees form a graph. Neighbours up to 12.6 m apart each send a limb toward the other, so a crossing is limb, then a 1.8–3 m reach between limb tips, then limb. Routes are found breadth-first through the graph (`TraverseCanopy`, and `CanopyTravel` at night). | INVENTED |
| Limb ambush | Picks a point along a low limb (about 4.3–6.6 m up) above the trail, game trails or a pool edge. Candidates are scored by fresh prey scent below them, the trail, a nearby pool edge, distance from the feared rival, and its memory: points it hung from in the last few days, or waited at in vain, score lower. The snake lies along the limb with its head hanging about 2.4 m down (`LieOnLimb`). It waits longer by day and moves between limbs mostly at dusk and at night; by day it only keeps a fresh limb (unless starving). | ADAPTED |
| Ground ambush | After several fruitless waits on the limbs at dusk or night, a hungry tree anaconda with the giant out of range changes tactics (`ChangeTactics` › `TryTheGround`): for the rest of that night it lies coiled beside the trail or at the pool edge near the grove (`GroundAmbush`: ChooseGroundSite › GoToGroundSite › CoilByTrail) and strikes from there. At dawn it goes back up. | ADAPTED |
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

Four humans and four capybaras (two herds). Each animal is an individual: capybaras are young (a small meal) or adult, people differ in build. An eaten animal is replaced only after 3–5 game days (a newcomer from the island edge, away from the snakes). Both snakes hunt the same prey base, so after a few meals the island runs short, hunger climbs to starving, and the snakes widen their ranges and hunt on land. Prey watch for both snakes: awareness builds from whichever snake is most visible. Prey rarely notice a snake that lies still in a tree.

### Two-snake domain tasks and world-state bytes

- **Survive:** `AbandonKill` (Escape: `UpATree` / `FleeOverland`), `RegurgitateAndWithdraw` (now `WithdrawToWater` / `ClimbToSafety` / `FleeOverland`), `FreezeInCanopy`, `ClimbToSafety` (PlanOnly `InTree=1` then `FreezeInCanopy`), `Ball`, `StrikeBack`, `FleeOverland`, `FleeToWater`, all under `Survive › Escape` (threatened, or a feared rival close). `Survive › Vigilance` (wary of people) is described under "Days that differ".
- **Feed** is now a selector: `EatKill` (ReachWater › Kill › Swallow), `Scavenge` (GoToCarcass › TakeCarcass), `TakeOverKill` (ApproachRivalKill › PausePlan).
- **Hunt:** `StrikeRival`, land branches that also need "safe to come down", `TreeAmbush` (`LieOnLimb`, or `RelocateToLimb`: ChooseAmbushLimb › GetIntoCanopy [AlreadyInTree / ClimbTrunk with PlanOnly `InTree=1`] › TraverseCanopy › LieOnLimb), `ChangeTactics` and `GroundAmbush` (tree anaconda), and AquaticLieInWait (giant and male only).
- **Digest:** `DigestAtTreeBase`, then the tree anaconda's perch branches, then the water-side branches (not for the tree anaconda).
- **Thermoregulate:** `BaskOnLimb`, `ShadeInCrown`, then the ground and water branches.
- **Ambient:** `ClimbHome`, `CanopyTravel`, and `RestInCanopy` in DayLieUp.
- **Rival bytes:** `RivalNear` (none / sensed / near <20 m / close <12 m, with distance and confidence hysteresis and a 6 s hold), `RivalIntent` (from the board), `RivalKill` (the rival holds a kill on the ground within 35 m and is sensed), `Carcass` (a known carcass that is free). A change of `RivalNear` for a feared rival may cut short a committed task's dwell, and "feared rival not close" is an urgent executing condition on the limb tasks.

## Days that differ: weather, places, memory and routines

The aim is that no two days look alike and no pattern is easy to spot, without dice deciding behaviour. Variation comes from the world state the planner sees, and every choice goes through the HTN (new branches with conditions, world-state bytes with hysteresis, executing conditions, MTR).

**Weather and insolation.** Cloud cover drifts toward a "front" that changes every 5–16 game hours, so spells of clear, overcast and rainy weather last hours. Each calendar day also has its own temperature offset (a warm or cool spell carried over from the day before). Insolation (sun elevation × cloud) drives body heat, how worthwhile basking is, how fast a meal is digested, and prey routines. The quantised sky is the `Sky` byte (clear / overcast / rain, with hysteresis).

**How to rest: the `RestPref` byte.** The quantiser scores four ways of resting: *sun* (needs the sun more than 25% up, no rain, and enough day left; worth more when the snake is cool and the sun strong), *dappled* (mild sun, overcast is fine), *cover* (worth more after disturbance, by day, in rain), and *cool* (when hot, in rain, at night, or after disturbance). Each snake also has a slowly drifting mood per way of resting, and a way of resting it has used for hours loses a little appeal. The current choice is held for at least 4.5 game hours unless it becomes impossible, and is only replaced by a clearly better one. Digest, Thermoregulate and DayLieUp branch on it:

- Pool snake digesting: `BaskOnBank` / `FloatInShallows` (back at the surface in the sun) / `RestInReedsAtEdge` / `RestSubmergedDeep`.
- Tree anaconda digesting: `DigestOnSunnyLimb` / `DigestAlongLimb` / `DigestInFork` / `DigestInShade`.
- DayLieUp (not hungry, not digesting): `LieUpOnBank` / `LieUpFloating` / `LieUpInReeds` / `RestSubmerged`.
- `Thermoregulate › WarmUp` basks until the snake reaches its preferred temperature, and only when the sun is worth it. When it is cold with no sun and the water is warmer than the air, `WarmInWater` rests submerged instead.
- `Digest › NightReposition`: a light digester that is restless (a day or more at one spot, scared there, or getting peckish) moves at night, along the shore or to another pool (pool snake), or to another crown (tree anaconda).

**Where to rest, wait and hang: sites with memory.** `ChooseBaskSite`, `ChooseShallows`, `ChooseReedSite`, `ChooseDeepSite`, `ChooseRestSite` (night), `ChooseWaterSite`, `ChooseAmbushLimb`, `ChooseCrown` and the perch choice score many candidate spots:

- Rest spots are about 14 points around each reachable pool, scored by sun now (tree shade, and which way the bank faces the sun), cover (reeds, tall grass, trees), distance (close by day), the paths people use, the rival, scare memory, and how recently it used a spot.
- It may simply stay, or change the way it rests where it lies. For example, it surfaces to float where it was resting submerged, or stays on its limb and lies differently. A rest that is only relabelled (for example day lie-up becoming night rest at dusk) keeps the existing coil instead of swimming a new loop.
- Coil size and turns vary with the room there; basking and floating snakes lie in loose loops.
- Ambush spots come from fresh scent at the shore, plus the pool's prey traffic over the past days (`poolMem`, built from everything the snake sensed near each pool, fading over ~2.5 days), minus spots used recently, spots where it waited in vain or missed, and spots where it was scared. By day it chooses near where it lies.

**Hunting fails.**

- After a fruitless wait by day, a non-starving snake lies up until dusk (`Hunt › GiveUpAndRest`, `HuntMood` = fasting). At night it tries 2–3 sites first, then fasts for hours. At night, after a wait in vain, it prowls the shallows with its tongue before settling again (`ShorelineForage`).
- There is no open-mouth display before a predatory strike: a submerged snake bursts out, a tree anaconda drops, and on land the snake only braces its neck (`StrikeFromCover`). Strikes miss more often at alert prey, at the limit of reach, and in the open; capybaras are jumpy.
- After a miss, `MissRecovery` runs `RecoverFromMiss`: it slides back under or freezes, watches, and marks the place as burned. Then a cool-down of 1–2 game hours follows. Only the small male may strike once more at close, unwary prey; the giant and the tree anaconda never strike twice in a row.
- A stalk is given up when the quarry has clearly noticed the snake, or after a while without closing in.
- Opportunism: a snake that is only peckish (and not digesting) still takes prey that walks into its strike window.

**Meals differ.**

- Constriction lasts until the prey stops struggling. That depends on its mass and individual strength, how well it is wrapped, the snake's size, and drowning in water. Struggles can flare up again.
- Swallowing takes several times longer than the kill: bouts of jaw-walking with pauses, scaled by prey girth against the snake. A half-swallowed prey keeps its progress if the plan is rebuilt. A threat can still make the snake regurgitate.
- Digestion length scales with prey mass, and digestion runs faster when the snake is warm. Body condition (reserves from recent meals, slowly used up) sets how quickly hunger returns, so intervals between meals vary with the snake's history.

**Moving like a snake.**

- Long moves follow an A* route over a 3 m cost grid. Pool water, the shore margin, reeds, tall grass and tree cover are cheap. Open grass, paths and camp are expensive by day and cheaper at night.
- Moves inside one pool follow the shallows in an arc. The heading wanders a little and the speed surges.
- On land the snake travels stop-and-go. It halts at the edge of cover before crossing open ground, and now and then in the open, flicking its tongue, for longer when it is uneasy. Swimming, it pauses now and then.
- Going home happens by night (or when far out). Inter-pool travel by day is avoided.
- Nights are the main time of movement: ambushes are longer by day and shorter at night, with night prowls and patrols.

**Disturbance, habituation and sensitisation.**

- Each person the snake feels or sees within ~15 m adds stress by distance, approach and speed. This is weighted by how exposed the snake is: an open bank more than reeds, reeds more than afloat, afloat more than submerged, and submerged more than up a tree. A hunting snake reads people as quarry instead.
- People who pass without incident habituate it. A scare (stress reaching "wary" or "threatened") sensitises it and is remembered at that spot for days, and rest-site choice avoids the spot.
- `Survive` has two levels. `Escape` covers threatened, or a feared rival close (the old branches). `Vigilance` covers wary of people: `SlipIntoWater` › `SinkDeeper` from a bank, `SinkDeeper` in water, `FreezeOnLimb` in a tree, or `FreezeInCover`. The snake then lingers until it has been calm for a while.

**Prey routines.**

- People sleep at camp most of the night, work at the terminals, and visit pools by day according to their own (slowly drifting) habits. Sometimes they stroll along a shore, and they shelter in rain.
- A person who has spotted a snake twice at a pool avoids that pool for a day or two. Everyone avoids a pool where someone was taken for 1.5–3 days.
- Capybara herds plan each day around midnight. They choose a pool (familiar pool, the day's forage, distance, and a multi-day memory of danger at each pool), and they drink at varied times around dawn and dusk (sometimes skipping, sometimes at midday on hot days) and wallow on warm days.
- A herd that sees a snake lying in the open grazes away from it without panic. A kill site is feared for 2–4 game days.

## Deviations from the plan

- **Hunt condition uses `Digesting < 2`, not `Digesting = 0`.** The plan's §6.5 says Hunt becomes valid again at "sluggish" (1), but §6.2 gives the condition as `= 0`. I followed §6.5. Hunt operators move at 0.6× speed while digesting.
- **Hunt also requires `fatigue < 0.85`, and StrikeNow requires that no miss happened in the last 1–2 game hours** (the small male may follow up once at close, unwary prey). This is the plan's fatigue "risk/energy budget" made concrete. Without it, a snake that keeps missing would keep striking forever.
- **AmbushCutOff waits 30 s after a failed cut-off.** This stops it from re-planning the same impossible intercept over and over.
- **Prey awareness builds up over time** instead of noticing the snake instantly. It builds slower when the snake is lying still or is in grass, and people see much less at dawn, dusk and night. Prey react to a lunge after about 0.25 s. Without these, prey always escaped. A submerged snake cannot be seen. Some strikes miss on purpose: alert prey, prey at the limit of reach, or prey struck in the open sometimes leap aside (overall about 30–60% of strikes succeed).
- **Prey remember danger.** Places where a snake was seen or struck go on a fear map for 4 minutes (a kill site for 2–4 game days), and prey avoid them. Humans who have seen the snake become vigilant, warn people nearby, and avoid the pools for a while (a pool where they met it twice for a day or two). When a person is caught, the others run to camp and regroup there. Eaten prey come back after 3–5 game days, from the island edge on the far side from the snakes.
- **Capybaras live in herds.** Each herd grazes slowly around one anchor point (about 0.3 m/s), with its members 2–4 m apart. Each day it picks a pool and drinks at varied times, mostly around dawn and dusk, and wallows on warm days. When one capybara takes alarm, the whole herd flees together, into the nearest pool away from the snake. If the snake is in that pool, they flee onto land instead. A herd whose shore is all feared moves to another pool. Capybaras swim; humans do not.
- **Meals last days.** An adult capybara takes about 6 game days to digest, a young one 2–3, and a person about 8, faster when the snake is warm. The small male digests 1.5× faster. The snake starts hungry so the first hunt happens early.
- **The Breed, RefugeDuringShed and DrySeasonCluster branches are in the tree but never run.** Mating is deliberately not implemented, and there is no shed cycle or dry-season phase.
- **Only the tree anaconda climbs.** The small male's `RestInTree` still rests at the base of the trunk. The tree anaconda climbs only the six big grove trees (the other trees are too thin to carry it).
- **The tree anaconda is much bigger than a real arboreal anaconda** (about 21 m), because everything here is scaled to the ~36 m giant. Being smaller, it takes longer to kill and to swallow the same prey than the giant does (roughly 25–90 s and 100–250 s, against 5–25 s and 15–125 s), which also gives a rival time to notice the kill.
- **Two snakes, one Director.** Plan §10 has the Director pick the best eligible snake. Here that means the nearest one that is alive, not carrying a kill, and not mid-strike.
- **Capybaras are included as non-player prey,** as raised in open question 11.
- **Heavy kills far from water are eaten on the spot.** If the nearest water is more than 80 m away, the snake swallows there instead of dragging.
- **Time is compressed:** one day is 360 game seconds. Speed buttons (1×, 3×, 10×, 30×) multiply that.
- **Added world-state bytes:** `Sky` (clear / overcast / rain), `RestPref` (sun / dappled / cover / cool) and `HuntMood` (keen / fasting) are not in the plan's list of 21. They make weather, the choice of how to rest, and giving up a hunt visible to the planner and in the panel.
- **Added drive:** body condition (reserves) is not in the plan's list of drives. It changes only how fast hunger returns.
