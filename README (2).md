# PAIRATES

**Four AI pirate crews compete for treasure in a shared sea. They share one brain — the only thing that differs between them is nine numbers. Those numbers evolve between rounds based on who won.**

▶ **[Run it in your browser](https://hmac10.github.io/pairates/)** — no install, no signup, one HTML file.

---

## What this actually is

Every crew runs the same `think()` function. Ironhold doesn't have "raider logic" and Goldhaven doesn't have "hoarder logic" — they have different values for the same nine parameters, and their personalities fall out of that.

A round ends when a faction banks 100 chests. That result is the entire reward signal: the four are ranked, the winner's parameters jitter in place, and the losers step toward the winner plus gaussian noise. Then the sea regenerates, the strongholds shuffle corners, and it runs again.

The point isn't the pirates. The point is watching what happens to four hand-authored personalities when you let a competitive process edit them a few dozen times.

## What happened when I ran it

Two findings from my own runs. Both are single-seed and the variance is real — run it yourself and see if you get the same.

**The personalities dissolve.** Population diversity — the spread between the four factions, summed across traits — collapsed from 4.26 to 1.54 in six generations. Everyone chased whoever was winning, so they converged on roughly the same pirate. The four distinct crews you start with are a temporary condition.

**Chasing the prize is a losing strategy.** A golden chest surfaces in the middle of the map every 90 seconds, worth six ordinary chests. In one 25-minute run, Tempest (ambition 0.92) grabbed it twelve times and Goldhaven (ambition 0.25) grabbed it once. Goldhaven won, 92–75. Ten carriers were sunk while holding it. The middle of the map is the most contested water on the board, and carrying the prize paints a target on your back — rival crews will cross the map for you.

I didn't design that outcome. It's what the numbers did.

## The nine parameters

| | |
|---|---|
| `caution` | probability of running rather than fighting |
| `greed` | how full the hold gets before sailing home |
| `aggression` | weight toward hunting over gathering |
| `reach` | how far from home she'll sail |
| `repair` | how damaged before she spends 5s in dock for 75 hull |
| `ambition` | how hard she bites on the golden chest |
| `helm` | rudder authority — turns harder, but the rudder drags |
| `spacing` | how much room she gives other hulls |
| `standoff` | the range she fights at, 8–23 units |

Every one has a genuine trade-off on both sides. Anything that was strictly better got hard-coded instead, because a parameter with an obvious optimum teaches you nothing and just adds noise to a small experiment.

## What's in the sim

- **Broadside gunnery.** A gun only bears across the beam, so ships have to work themselves abeam of their prey — a bow-on approach can never fire. Manning the gun is a task a crewman walks to. Shots are ballistic and miss about half the time; crews read the target's course by eye, so only part of the true lead is applied and error grows with range.
- **Crews as agents.** Nothing is instant. Fishing a chest out of the water means a hand walks to the fishing station and works it for two seconds with a progress ring over his head. Unloading means porters carrying chests one at a time down the pier and through the gate — score only counts when the treasure physically arrives.
- **A shipyard.** Sunk ships aren't respawned, they're rebuilt: scaffolding heaves out of the water, hands work a ribbed hull frame for ten seconds, then she launches. Four berths per stronghold, shared between mooring, building and refitting — lose two ships and you're genuinely short of dock space.
- **Galleons.** Dock with six hands and she's laid up and rebuilt bigger: an extra deck, sixteen guns across two tiers instead of six, double the hold, 180 hull instead of 100. Sink one and she comes back off the stocks as a sloop.
- **A generated archipelago.** New islands every round, five silhouettes, flood-filled at generation time to guarantee no faction is walled into its corner.

## Controls

| | |
|---|---|
| drag / scroll / right-drag | orbit, zoom, pan |
| click a ship | inspect her |
| `F` | free camera — WASD to fly, drag to look, Space/Shift for altitude |
| **Stats** | floating hull, orders and crew over every ship |
| **Evolution lab** (top right) | the experiment |

The lab holds a personality radar, per-trait drift charts, a win tally, sixteen sliders to hand-set any faction's parameters mid-run, mutation and imitation strength, treasure scarcity, and a **Summary** view with the full run. **Download data** exports every generation as CSV — winner, duration, all scores, all parameters — so you can plot it yourself.

## What to call this

It's **evolutionary computation**, specifically an *evolution strategy*: real-valued parameter vectors, gaussian mutation, selection by rank. It is **not** machine learning in the usual sense — there's no gradient, no loss function, no model being fit. It's derivative-free black-box optimisation.

The most precise term is **competitive coevolution**: fitness comes from competing against the other populations rather than a fixed objective, so the landscape moves as your opponents adapt. That's the **Red Queen effect**, and it's why everything converges — the target keeps running.

## Technical

Single HTML file. Three.js r128 from a CDN. No build step, no bundler, no backend, no assets. Clone it and double-click it.

A few decisions worth knowing, since they look like oversights otherwise:

- **`OrbitControls` doesn't exist in r128.** The camera rig is hand-rolled on purpose.
- **The wave maths is duplicated in JS and GLSL.** The GPU draws the ocean; the CPU needs identical heights so ships float correctly. Change one and you must change the other.
- **Placement uses measured bounding boxes**, not hardcoded coordinates. `seat()` and `stack()` put a part on another part's measured top, so nothing floats or clips.
- **There's a win condition,** which was originally a non-goal. It was added because the reward function needs something to score. It ends a *round*; the player still never wins anything.

## Known gaps

Storms as a regional world lever. Best-of-three rounds per generation — with nine traits and one round of evidence per generation, the drift charts are noisier than I'd like, and this is the fix, at triple the runtime.

## License

MIT. Do whatever you like with it.
