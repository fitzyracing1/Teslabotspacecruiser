# TSB-01 — Hex Panel Interface Control Document

**Tesla Bot Space Cruiser · Panel ICD Rev A · Almeida**

The TSB-01 is a modular spacecraft assembled on orbit from standardized hex panels
by Optimus-class humanoids. A vehicle of this architecture is never launched as one
piece. It is launched as a stack of panels and becomes a ship after it arrives.

That makes the panel — not the ship — the thing that has to be specified first.
This document is that specification. Everything else in the program (fairing packing,
launch cost, assembly time, robot reach envelope) falls out of the numbers below.

> **Status:** Rev A is a paper baseline. Every number is a starting allocation, not a
> measured value. Values marked `[TBR]` are To Be Resolved and are the ones most
> likely to move. Nothing here has been built or tested.

---

## 1. Panel geometry

| Parameter | Value | Note |
|---|---|---|
| Form | Regular hexagon | |
| Flat-to-flat (F2F) | 1.100 m | Datum dimension |
| Side length | 0.635 m | Derived, F2F / √3 |
| Vertex-to-vertex | 1.270 m | Governs fairing packing |
| Face area | 1.048 m² | |
| Stack thickness | 0.120 m | Panel + retracted interlock |
| Edges | 6, all identical | Genderless — any edge mates any edge |

**Genderless edges are non-negotiable.** If edges have a male and female variant, a
robot can arrive at a joint holding the wrong one, and on-orbit that is an
unrecoverable assembly fault. All six edges are mechanically and electrically
identical and self-orienting.

### 1.1 The pentagon problem

Hexagons tile a **flat plane**. They cannot close a convex hull. By Euler's formula,
any closed hexagonal shell requires exactly **twelve pentagons** — this is why a
football is not made only of hexagons, and why a geodesic dome has twelve
five-way nodes.

So the panel family is not one part. It is two:

- **HEX-1** — the tiling panel, spec'd above
- **PEN-1** — a pentagonal panel, same edge length (0.635 m), same interlock, same
  bus. Twelve required per closed hull.

Any TSB-01 hull that closes must carry twelve PEN-1. A hull that is open (a flat
solar wing, a truss face, a radiator plane) needs none. **This is the single most
important geometric consequence of the hex-grid choice and it is missing from the
current schematics.**

---

## 2. Mass budget

Areal density assumes a CFRP-facesheet aluminium honeycomb sandwich with an
embedded power/data harness and a thermal doubler layer.

| Item | Mass | Basis |
|---|---|---|
| Panel body @ 15 kg/m² `[TBR]` | 15.7 kg | Sandwich + harness + doubler |
| Interlock hardware, 6 × 1.2 kg | 7.2 kg | Pins, clamp, connector, seal, actuator |
| **Current Best Estimate (CBE)** | **22.9 kg** | |
| Margin, 20% (Rev A maturity) | 4.6 kg | |
| **Maximum Expected Value (MEV)** | **27.5 kg** | **Use 28 kg for all planning** |

The 15 kg/m² figure is the number to attack first. A pure structural sandwich is
3–6 kg/m²; the rest is bus, connectors, and thermal. If the panel comes in at
10 kg/m², panels-per-launch rises by roughly a third and the whole program economics
change. If it comes in at 25 kg/m², this architecture is in trouble.

---

## 3. Launch packing

Sized against a **Falcon 9, reusable profile, dedicated launch**.

| Parameter | Value |
|---|---|
| F9 usable payload to LEO (droneship recovery) | ~16,000 kg |
| Dispenser / carrier structure, 20% | ~3,200 kg |
| Available panel mass | ~12,800 kg |
| **Panels per Falcon 9 @ 28 kg** | **~457** |

**Volume check.** Fairing usable envelope ≈ 4.6 m diameter × 8 m effective height.
Hex panels of 1.27 m vertex-to-vertex pack roughly 12 per layer with edge losses;
8 m ÷ 0.12 m stack thickness = 66 layers. Capacity ≈ **790 panels by volume**.

> **The panel is mass-limited, not volume-limited.** There is roughly 40% spare
> fairing volume at full mass. That spare volume is where the PEN-1s, the keel
> segments, the tooling, and the robots ride. Do not spend mass buying back volume
> you already have.

### 3.1 One launch, one hull

A 30 m × 6 m hull is on the order of 500 m² of surface. At 1.048 m² per panel that is
roughly **480 panels — approximately one Falcon 9.** This is the headline number for
the program and the reason the architecture is worth pursuing at all: the ship
arrives in one launch and assembles itself.

### 3.2 Rideshare, for the flight demo only

A single HEX-1 at 28 kg fits inside the SpaceX rideshare 50 kg entry block. A
two-panel mating demonstration (56 kg) prices at approximately **$392,000**
(current advertised rate: $350,000 for the first 50 kg, $7,000/kg thereafter).

**Do not buy this yet.** See §7.

---

## 4. Interlock

Three-stage capture. Each stage hands off to the next with a tighter tolerance,
because the robot cannot deliver final precision and should not be asked to.

| Stage | Travel | Lateral tol. | Angular tol. | Delivered by |
|---|---|---|---|---|
| 1 — Coarse capture | > 40 mm | ±25 mm | ±3.0° | Robot manipulator |
| 2 — Pin alignment | 40 → 10 mm | ±0.5 mm | ±0.5° | Tapered pins, 15° half-angle |
| 3 — Clamp preload | 10 → 0 mm | ±0.2 mm | ±0.1° | Powered clamp |

**Stage 1 is the requirement on the robot, and it is deliberately loose.** A humanoid
manipulator is not an industrial arm; assume free-space positioning of ±10–20 mm
before contact. The joint is designed to be compliant enough to forgive that. If
Stage 1 ever needs to be tighter than ±25 mm, the interlock is wrong, not the robot.

**Connectors mate last.** Blind-mate connectors sit on floating mounts with ±0.5 mm
radial compliance and engage only in the final 10 mm, after structural alignment is
established. Never let a connector take an alignment load.

**Every stage must be reversible under power.** A panel that cannot be commanded to
release is a permanent defect in the hull. The clamp actuator holds preload
unpowered (fail-safe closed) but must retract on command with a mechanical override
accessible to a gloved or robotic hand.

---

## 5. Power and data

### 5.1 Power

**120 VDC**, matching ISS US-segment practice. Lower bus voltages force very high
currents across hundreds of mechanical joints, and joint resistance is the enemy of
this entire architecture.

| Parameter | Value |
|---|---|
| Bus voltage | 120 VDC ±10% |
| Per-edge current limit | 30 A (3.6 kW) |
| Panel through-rating | 10 kW `[TBR]` — thermally limited, not conductor limited |
| Contact resistance budget, per edge | < 2 mΩ `[TBR]` |

A hex grid is a **mesh**, not a tree. Power has many paths from source to load, which
is excellent for fault tolerance and awkward for protection — a fault does not have
one upstream breaker. Every edge needs local current limiting and every panel needs
to be able to isolate any of its six edges independently.

### 5.2 Data

| Layer | Medium | Purpose |
|---|---|---|
| Primary | 1000BASE-T, 2 pairs | Vehicle network, mesh-routed |
| Sideband | CAN or RS-485, 2 wires | Survives when primary is down |

**The sideband is the safety interlock.** Before any edge is energized:

1. Neighbor asserts presence on the sideband
2. Neighbor reports its unique panel ID and its clamp state
3. Both panels confirm Stage 3 preload achieved
4. Only then does 120 V appear on the edge

A hot connector presented to a robot, or to a panel that is not actually clamped, is
the failure mode most likely to end this program. The sideband exists to make that
sequence impossible to skip.

---

## 6. Open items

These are unresolved and are listed here rather than hidden.

**6.1 Thermal isolation is the sleeper problem.** Six mechanical joints per panel
means each panel is close to a thermal island. Heat cannot spread through a bolted
interface the way it spreads through monocoque structure. Either every panel carries
its own radiator area — which is a large mass hit not in §2 — or the interlock needs
a conductive strap, which fights the requirement that the joint be cleanly
separable. **Unresolved. This is the item most likely to force a redesign.**

**6.2 Do not pressurize the hex grid — Rev A recommendation.** Sealing a six-edge
robotic joint to habitat pressure is far harder than it looks; this is precisely why
ISS uses a large single-gasket circular berthing mechanism rather than a tiled one.
Every hex vertex is a triple-point where three seals meet, and triple-points leak.
**Recommendation: HEX-1 and PEN-1 are unpressurized structure only. Pressurized
volume goes in dedicated modules that berth to the grid through a conventional
circular hatch.** Revisit only with test data.

**6.3 Assembly sequence and reachability.** Not addressed here. A partially built
hull has to be stable, and the robot has to be able to reach the next joint while
standing on the panels already placed. Needs a separate document.

**6.4 Panel-body areal density** — the 15 kg/m² in §2 is an allocation, not a
measurement.

---

## 7. What to do next

In order. Each step is cheap and each one can kill the architecture before the next
one costs real money.

**Step 1 — Machine one mating pair. Bench, 1 g, no robot.**
Two edge segments, full-scale, on a linear slide. Measure actual capture envelope
against the §4 tolerances. Cost: shop time. This either validates the whole concept
or ends it, for the price of two parts.

**Step 2 — Blind mating.** Same hardware, mated by an arm with deliberately injected
position error. Sweep the offset until capture fails. That number, not the one in
§4, becomes the real Stage 1 requirement.

**Step 3 — Thermal vacuum on the joint.** Contact resistance and joint conductance
across the real temperature range. Directly attacks open item 6.1.

**Step 4 — Six-panel flat array.** Smallest assembly that exercises mesh power
routing, mesh data routing, and a vertex where three panels meet.

**Step 5 — Only now consider the rideshare demo in §3.2.**

Steps 1 and 2 are achievable in a garage with a machine shop. Nothing above Step 3
should be funded until Steps 1 and 2 have produced a number.

---

## Repository housekeeping

Outstanding, unrelated to engineering:

- `docs/assets/schematics/main-overview.jpg` is a 20-byte text file, not an image.
  Delete it or replace it; nothing currently references it.
- `docs/index.html` still contains authoring artefacts addressed to the author
  rather than the reader — "Your uploaded images are now correctly displayed",
  the green "Fixed!" callout, "Your third uploaded schematic blueprint". Remove
  all three before showing this to anyone technical.
- Schematic labels contain typos: "Plasta" → "Plasma", "Optimuss" → "Optimus",
  "Scal:" → "Scale:".
- The schematics show warp nacelles. They are not costed, specified, or referenced
  anywhere in this document, and their presence alongside real engineering numbers
  will cost the project credibility with exactly the readers it needs. Recommend
  moving them to a clearly separated "far concept" page.

---

*Rev A · Paper baseline · No item herein has been built or tested.*
