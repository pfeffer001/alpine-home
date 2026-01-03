# Ventilation Analysis & Guidelines

## 1) Why Ventilation Is Not Optional Here

Wood can be “vapour open” (moisture diffusion) while the building envelope is still intentionally airtight (to reduce heat loss). In an airtight home you typically need controlled air exchange to achieve:

- **Air quality:** CO₂ rises quickly in bedrooms overnight; VOCs and odors also accumulate.
- **Moisture control:** showers/cooking/laundry create humidity peaks; you want reliable removal to reduce mold risk and protect the timber structure.
- **Comfort + efficiency:** continuous fresh air without relying on window airing (and without uncontrolled heat loss).

---

## 2) The Two Viable Architectures

### Option A — Centralized ventilation (one unit + duct network)

Typical example in your spec: Zehnder ComfoAir Q with ComfoConnect PRO, optionally paired with ComfoFond-L Q.

**When it fits well**

- You can reserve space for ducts, manifolds, silencers, and a vertical riser (“Schacht”).
- You want the quietest bedrooms (fans are remote in the tech room).
- You prefer one system to maintain and one integration point for HA.

**The timber-house constraint (statics + build coordination)**

Large rigid trunk ducts (e.g., 150–160mm) can conflict with a timber floor grid. For a timber build, the preferred “best practice” approach for a centralized system is to design around a **radial (manifold / “octopus”) system**:

- **Distribution box/manifold as a hub**
- **Semi-rigid round tubes (typically ~70–90mm, e.g., 75/90mm)** to each room
- Duct routing planned early (service voids, web-joists/posi-joists where used, and explicit riser locations)

**Note on tube diameter (70/75mm vs 90mm):** there isn’t a single “always best” size. As a practical default, use **75mm** for most bedrooms/offices and use **90mm** (or two parallel 75mm runs) for higher-flow rooms (often living/dining) and/or longer, bend-heavy routes. The goal is to keep pressure drop and air velocity low enough to stay quiet.

This keeps penetrations smaller and makes routing more “cables through a building” than “big pipes through joists”.

**Structural enabler: Posi-Joists (metal-web joists)**

Posi-Joists can help a lot for centralized ventilation because they create an intentional “services zone” within the floor depth.

- **Why they help:** the open web makes it much easier to pass multiple 75/90mm radial ducts (and other services) through the structure with fewer bespoke cutouts.
- **What they don’t replace:** you still need a planned vertical riser (Schacht), and you still need early coordination for manifolds, silencers, and lighting.
- **Practical caution:** service routing is typically easiest in the mid-span web area; areas near supports/bearings and around large openings may have restrictions.
- **Best combined approach:** Posi-Joists + radial ducts + (optional) small soffits only where you need transitions or where acoustic elements require space.

**Architectural workaround: “fake beams” / ceiling soffits (bulkheads)**
This can be a very practical way to keep a centralized system while avoiding risky cuts through the timber structure.

- **What it is:** a deliberate boxed-out zone in the ceiling (often along a corridor, wall line, or over cabinets) that hides larger ducts, silencers, or distribution transitions.
- **Why it helps:** it relocates the “big duct problem” from *inside joists* to *below the ceiling plane*, which is usually far easier to coordinate structurally.
- **Trade-offs:**
  - Aesthetic: you lose a perfectly flat ceiling line.
  - Planning: you must decide early (it affects lighting layout, sprinklers/smoke detectors, and sometimes doors/wardrobes).
  - Acoustics: ensure ducts in soffits still include silencers/liners as designed (don’t turn the soffit into a drum).
- **Where it works best:** hallways, entry zones, laundry/utility areas, along one “service spine”, or above kitchen cabinetry. Avoid running a big soffit through the middle of a primary bedroom if you can.
- **Detail to insist on:** access panels where serviceable components exist (filters are in the unit, but dampers/silencers/distribution boxes may need inspection).

**Acoustics guidance (centralized)**

- Mount the unit with **vibration decoupling** (avoid rigidly coupling to resonant timber walls).
- Place **silencers directly after the unit** on supply and extract.
- Radial layouts naturally reduce **room-to-room crosstalk**.

### Option B — Decentralized ventilation (room units through exterior walls)

This uses multiple paired “push-pull” units (brand examples often seen: inVENTer, Prana) installed per room.

**When it fits well**

- You want **minimal impact on the floor structure** (almost no internal duct routing).
- You have limited space for vertical risers or don’t want any duct boxing.

**Trade-offs**

- More devices and filters to maintain.
- Audible fan presence in rooms (usually acceptable, but typically not as silent as a remote centralized unit).
- Integration can be more fragmented (many units vs one gateway).

---

## 3) Decision Summary (Centralized vs Decentralized)


| Dimension                | Centralized (e.g., Zehnder)                                   | Decentralized (room units)           |
| -------------------------- | --------------------------------------------------------------- | -------------------------------------- |
| **Structural risk**      | Higher unless designed around radial routing + planned risers | Lower (no floor duct crossings)      |
| **Quiet bedrooms**       | Usually best                                                  | Good, but fan is in-room             |
| **Maintenance**          | One unit / one set of filters                                 | Many small filters/devices           |
| **Integration**          | One gateway (ComfoConnect)                                    | Many endpoints                       |
| **Long-term simplicity** | Excellent if planned early                                    | Excellent if you accept device count |

---

## 4) Room-Level Design Rules (applies to both)

### Supply vs extract placement

- **Supply (fresh air):** living/dining, bedrooms, office.
- **Extract (stale/humid air):** kitchen, bathrooms, WC, utility/tech areas (as appropriate).

### Air transfer (the “invisible duct”)

To move air from supply rooms to extract rooms you need transfer paths:

- Typical approach: **door undercuts** (often ~10–15mm) or transfer grilles.
- Plan this early because it affects doors, acoustics, and sometimes smoke/odor separation goals.

### Kitchen hood compatibility

- Prefer a **recirculating** kitchen hood (filtering) so it doesn’t fight the ventilation pressure balance.
- If you plan an exhaust-to-outside hood, it needs a deliberate make-up air concept and should be coordinated with the ventilation designer.

### Condensation + insulation in the tech room

- Outdoor intake/exhaust ducts and any “cold” lines must be **properly insulated and vapour sealed** to avoid condensation.
- Keep the ventilation layout serviceable and away from sensitive equipment where possible (rack/servers).

---

## 5) Home Assistant Integration Guidelines (aligned with alpine-home-spec.md)

### Control hierarchy

1. **Zehnder (or vendor system) runs the control loop:** frost protection, bypass, balancing, alarms.
2. **HA orchestrates modes only:** predictable, low-frequency commands.

### Recommended HA-visible controls (dashboard)

- Mode: `Home / Night / Away / Boost`
- A time-bounded override: `Boost for 30/60/120 min`
- A quiet rule: do not allow the loudest speeds at night

### Sensor-driven adjustments (keep it conservative)

You already plan 6x Eve Room sensors. A simple, stable rule is:

- If `Bedroom CO2` > 1000 ppm for 10–15 min during night → raise ventilation to “Medium”
- Cap the maximum night speed to avoid noise

---

## 6) Floor-by-Floor Intent (example mapping)

Ground floor (mixed social/service)

- Living/dining: **Supply** (in high-ceiling spaces, supply placement should avoid drafts; ceiling/high-wall supply is common)
- Kitchen: **Extract**
- WC / utility / tech: **Extract**

Upper floor (private/rest)

- Bedrooms: **Supply** (place supply so air washes the sleeping area before exiting under the door)
- Bathroom: **Extract** (humidity peaks should trigger vendor boost automatically; HA can mirror/override as a mode)

---

## 7) Questions / Checklist for Architect + HVAC Planner

Structural / routing

- [ ] Confirm whether a **centralized radial duct system** is structurally routable with the planned floor design (service voids, joist type, allowable penetrations).
- [ ] If considering **Posi-Joists**: confirm the joist type, depth, and routing rules (where services may pass; restrictions near supports/openings).
- [ ] Identify the **vertical riser location(s)** from tech room to upper floor (avoid bedroom walls if possible).
- [ ] Confirm space for distribution boxes/manifolds and silencers.

Acoustics

- [ ] Specify vibration decoupling for the ventilation unit.
- [ ] Include silencers on supply/extract at the unit.

Doors / transfer

- [ ] Confirm door undercuts or transfer grilles are acceptable (target gap often ~10–15mm).

Kitchen

- [ ] Confirm recirculating hood choice (or coordinate an exhaust hood with make-up air).

ComfoFond / ground loop (if used)

- [ ] Coordinate trenching and routing early (foundation work is the easiest moment).
- [ ] Ensure pipes/ducts entering the tech room are vapour sealed insulated.

---

## 8) Financial & Operational Analysis

This section adds a financial/operations lens to the technical guidance above.

**Assumptions (please validate with installer quotes)**

- Region: Austria / DACH market, 2026 pricing.
- Currency: **EUR (€)**.
- Centralized example: Zehnder ComfoAir Q class system with radial distribution.
- Decentralized example: ~4–6 room units (count depends on rooms and required airflow).
- Figures below are order-of-magnitude ranges; exact outcomes vary with duct routing complexity, wall coring costs, and acoustic measures.

### 8.1 Ten-year cost comparison (budgetary)


| Expense category              | Centralized (e.g., Zehnder ComfoAir Q)                | Decentralized (wall units)                     |
| ------------------------------- | ------------------------------------------------------- | ------------------------------------------------ |
| Initial hardware              | ~€7,500–€9,000                                     | ~€3,500–€5,000 (4–6 units)                 |
| Ducting & installation        | ~€4,000–€6,000 (radial pipes + installation)       | ~€1,500–€2,500 (wall coring + installation) |
| Total upfront cost            | ~€11,500–€15,000                                   | ~€5,000–€7,500                              |
| Annual electricity            | ~€60–€100                                          | ~€40–€80                                    |
| Annual filter changes         | ~€80–€150 (1 set)                                  | ~€100–€200 (multiple sets)                  |
| Pro maintenance (every 3–5y) | ~€250–€600 (inspection/cleaning; varies by region) | ~€0–€300 (often DIY, but depends on unit)   |
| Ten-year running cost (rough) | ~€1,800–€3,500                                     | ~€1,400–€3,000                              |
| Total 10-year TCO (rough)     | ~€13,300–€18,500                                   | ~€6,400–€10,500                             |

### 8.2 Maintenance effort: DIY vs professional

Centralized (the “single system” path)

- Monthly: none
- Twice per year (DIY): swap filters in the tech room unit
- Every 3–5 years (pro): inspection/cleaning depending on dust, pets, renovations

Decentralized (the “many small units” path)

- Monthly: none
- Twice per year (DIY): visit each room unit; rinse/replace filters
- Every 1–2 years (DIY): clean/inspect the heat-exchanger core (model dependent)

### 8.3 “Cost of doing nothing” (risk lens)

Skipping ventilation can reduce upfront cost, but it increases operational and structural risk in an airtight timber house:

- **Moisture risk:** persistent humidity peaks in wet rooms raise mold/rot risk; remediation can be expensive and disruptive.
- **Energy waste risk:** keeping air quality acceptable via frequent window airing often increases heating demand (exact impact depends on habits and weather).
- **Comfort/health risk:** bedroom CO₂ can rise above ~1,000 ppm overnight without controlled exchange, impacting sleep quality.

### 8.4 Decision inputs (how to interpret the numbers)

- Centralized has a higher upfront cost but tends to be quieter and “one system to maintain”.
- Decentralized has lower build risk and lower upfront cost, but more in-room devices/filters to maintain.

---

## 9) Practical Recommendation for the Alpine Home (Technical + Financial)

- **Best overall (if centralized is feasible):** a **radial (“octopus”) centralized system** using ~**70–90mm** tubes to rooms, with a planned riser + acoustic measures. It best matches the comfort/quiet goals *and* keeps maintenance centralized (single unit, simple filter routine).
- **Best-value pivot (if routing is contentious or budget is tight):** a **decentralized approach**. It reduces structural coordination risk and upfront cost, at the expense of more devices and distributed filter cleaning.
- **Tie-breaker:** if you can solve routing cleanly using **Posi-Joists and/or planned soffits (“fake beams”)**, centralized becomes much more realistic without compromising the timber structure.
