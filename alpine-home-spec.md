# Alpine Smart Home

## 1) Vision

Build a smart home that is:

- **Safe**: early detection + fast mitigation (water/fire/CO), clear manual overrides.
- **Secure**: hardened network + secure access control + least-privilege IoT.
- **Efficient**: optimize heat, ventilation, and EV charging using real energy data.
- **Excellent atmosphere**: high-quality lighting + comfortable air + quiet, reliable automation.

## 2) Guiding principles

- **Local-first**: core functions should not require cloud or internet.
- **Fail-safe defaults**: critical systems define behavior during HA/network/power outages.
- **Wired where possible**: Ethernet/PoE backbone, wired contacts for openings.
- **Documented + maintainable**: labeling, backups, update policy.

## 2a) Reliability & maintainability contract (avoid endless tuning)

This project should remain reliable with **minimal ongoing configuration**. To make that true, use these guardrails:

- **Keep control loops in the vendor systems**: Buderus/Zehnder/Fronius should run their own stable control; Home Assistant orchestrates *modes* and *setpoints*, not second-by-second regulation.
- **Prefer simple, explainable automations**: schedules, scenes, and threshold rules with hysteresis/delays (avoid complex PID-style tuning).
- **Any automation must be safe to ignore**: manual switches and vendor apps/panels should still work if HA is down.
- **Make “default state” comfortable**: the house should feel normal without automations; automations add convenience and optimization.
- **Minimize radio/protocol diversity**: Thread for sensors where possible; avoid mixing many Wi‑Fi IoT vendors unless needed.
- **One source of truth**: pick one place for each function (e.g., lighting scenes in HA; thermostat schedules in vendor system).

## 2b) Automation scope (what we will / won’t do)

To keep maintenance low, define the boundary explicitly:

**Will do**

- Safety automations (leak → notify/optionally shut off water; smoke/CO → pathway lights).
- Lighting scenes and time-of-day behavior (simple and deterministic).
- Mode switching (Home/Away/Night) that changes setpoints rather than micromanaging devices.
- PV surplus rules that are coarse and stable (e.g., start EV charge above $X$ kW surplus for $Y$ minutes).

**Won’t do (by default)**

- Continuous “smart” optimization that depends on many sensors and frequent tweaking.
- Occupancy AI rules that turn HVAC on/off frequently.
- Fully automated heating curves beyond what the heat pump controller already provides.

## 3) Key assumptions (current)

- **Shelly relays are for lighting circuits only** (not for general Schuko socket circuits).
- **Cloud use is acceptable for surveillance**, but local operation is still preferred for basic functionality (recording/notifications policy TBD).
- Home Assistant is the integration hub.

## 4) Home Intelligence Central (Rack)

**Rack & power**

- Rack: Digitus 10" 9U Wall Mount
- Patch: 2x Digitus 10" 12-Port Keystone Panel
- Patch cables: 24x 0.25m slim-line
- Power strip: Digitus 10" Socket Strip (4x Schuko)
- UPS: Eaton 3S 700 DIN (external placement)

**Network**

- PoE Switch A: Ubiquiti UniFi Switch Ultra + with 210W Power Adapter (USW-Ultra-210W)
- PoE Switch B: Ubiquiti UniFi Switch Lite 8 PoE
- Internet router: TBD (ISP provided)

**Wi‑Fi**

- In-house APs: 2x UniFi U7 Pro Max (PoE, ceiling-mounted)
- Garden AP: 1x UniFi U6 Mesh (PoE, outdoor-rated; install under eaves facing garden)
- Provisioning principle: plan at least 1 ceiling AP per floor (CAT6 run to rack + PoE); adjust count after a site survey

**Compute & radios**

- Home Assistant server: Minix Z150-0dB (Intel N150, 16GB RAM, 512GB SSD)
- Thread adapter: Home Assistant Connect ZBT-2 (USB extension)

**Open design items**

- VLAN / segmentation plan (Trusted / IoT / Cameras / Guest)
- Remote access approach (prefer VPN vs port forwards)
- UPS runtime target + what is on UPS (router/ONT, switches, HA, intercom)

## 5) Electric energy management

- Hybrid inverter: Fronius Gen24
- Power meter: Fronius Smart Meter TS 65A-3
- Battery: Fronius Reserva
- Solar panels: SunPower Maxeon 6 or 7
- EV charger: Fronius Wattpilot Flex Home 22kW

**Automation intents (examples)**

- PV surplus → EV charging / DHW heating priority.
- Peak shaving using battery (policy depends on tariffs).

## 6) Heat energy & air quality

**Heating / DHW**

- Heat pump: Buderus Logatherm WLW176i-5 AR TP70
- System Controller: Buderus MX400 (required for buffer tank hydraulics)
- Controller: Buderus Logamatic RC100.2 (H)
- External DHW tank: Buderus Logalux SH290
- Gateway: EMS-ESP via BBQKeys Gateway E32 V2 (for HA communication)
- Heating actuator relays: Shelly Pro 4PM
- Thermal actuators: Möhlenhoff Alpha 5 (230V NC)

Notes: 
- The MX400 is required by the installer for the specified hydraulic design (with buffer tank). It is not replaced by the EMS-ESP gateway.
- The EMS-ESP gateway allows Home Assistant to monitor the system and orchestrate setpoints/modes.
- A physical room controller (e.g., Buderus RC100/RC200) is still recommended for commissioning and as a reliable manual fallback.
- **Hierarchy of Control:** To prevent conflicts, the Buderus controller (RC100) will own the primary heating schedule. Home Assistant will act as a supervisor, sending high-level commands for exceptions (e.g., "Away Mode," "Vacation Mode," temporary boosts). Manual changes on the physical controller will be respected by Home Assistant and not overridden automatically.

**Indoor / outdoor sensing**

- Indoor air quality: 6x Eve Room
- Outdoor weather: Tempest Weather Station

**Blinds**

- Blinds control: Shelly 2PM Gen4

### Ventilation

- Ventilation unit: Zehnder ComfoAir Q
- Gateway: Zehnder ComfoConnect PRO
- Air heat exchanger: Zehnder ComfoFond-L Q
- Zehnder Option Box V2

**Automation intents (examples)**

- CO₂/humidity-driven ventilation setpoints (bedrooms/night focus).
- Wind/rain-aware blind protection.

## 7) Lighting (and only lighting)

### Switching

- Light switch (8A): Shelly 1 Mini Gen4
- Light switch + power monitoring (8A): Shelly 1PM Mini Gen4
- Dry-contact relay (for control signals): Shelly 1 Gen4

### Dimming

- Light dimmer: Shelly Dimmer Gen4

**Notes to validate with electrician / fixture selection**

- Confirm **neutral availability** and **mounting space** behind switches/junction boxes.
- Validate LED **dimmer compatibility** (driver type, minimum load, flicker performance).
- Validate LED **inrush current** vs relay/dimmer limits for each circuit group.

## 8) Safety

### Water pipelines (consumption monitor + shutoff)

- Main meter: Diehl Auriga
- Pulse module: IZAR PULSE i (inductive clip-on)
- Monitoring node: Shelly Plus Uni (DIN-rail housing)
- Shut-off valve: U.S. Solid Motorized Ball Valve (24V DC, 1" Stainless Steel, 2-Wire Auto Return)
- Valve control: Shelly Pro 2 (dedicated or spare channel)

**Key decisions to document**
- Shut-off valve: U.S. Solid Motorized Ball Valve (fail-open) - to be dicussed with plumber!!
- Valve “safe state” during power loss (fail-open vs fail-closed).
- Manual override / bypass plan.

### Water leakage sensors

- Tech room: 1x Eve Water Guard (Matter over Thread, 2m cable)
- Kitchen: 1x Eve Water Guard (Matter over Thread, 2m cable)
- Bathroom: 1–2x Aqara Water Leak Sensor P2 (Matter over Thread)

### Fire safety (insurance compliant)

- Bridge: Bosch Smart Home Controller II (Ethernet)
- Smoke: 5x Bosch Smoke Alarm II (interconnected, VdS)
- CO: 1x Bosch Carbon Monoxide Alarm (living room / fireplace)
- CO: 1x Bosch Carbon Monoxide Alarm (technical room)

## 9) Security

### Surveillance

- Garden camera: AXIS M3116-LVE

**Open decisions**

- Cloud vs local recording/NVR and retention policy.
- Privacy boundaries (where cameras are allowed, masking, access controls).

### Entrance

- Intercom: 2N IP Verso 2.0
- Smart lock cylinder: 2N Fortis Cylinder
- Open window sensor (Thread): Eve Door & Window
- Open windows/doors (wired): ABUS FU7350W
- Presence sensors: 6x Aqara Presence Sensor FP2 (verify: typically Wi‑Fi)

### Gate

- Gate control: Shelly Pro 2
- Gate sensors: 2x 24V DC coupling relays
- Magnetic switch: 2x Seco-Larm SM-226L-3Q

## 10) Core scenarios (to implement intentionally)

- **Water leak** detected → notify + (optional) shut off main water + log event.
- **Smoke/CO alarm** → notify + turn on pathway lighting + unlock/egress policy (if desired).
- **Night mode** → reduced ventilation noise + gentle pathway lighting + privacy blinds.
- **Away mode** → security posture + leak monitoring + temperature bounds (via presence detection).
- **Coming home** → pre-heat house to comfort temperature before arrival (via presence detection).
- **PV surplus** → EV charging / DHW / battery policy.

## 10a) Commissioning plan (how to make it stable)

- **Phase 1: Make each subsystem correct standalone**

  - Heat pump + DHW runs correctly via Buderus controls.
  - Ventilation runs correctly via Zehnder controls.
  - Fronius energy flows visible and correct.
  - Lighting circuits behave correctly via physical switches.
- **Phase 2: Integrate into Home Assistant without changing behavior**

  - Only read states/metrics first.
  - Then add the minimal scenarios (water leak, smoke/CO pathway, basic scenes).
- **Phase 3: Add efficiency automation last**

  - PV surplus rules with conservative thresholds and timers.
  - Change one thing at a time; keep a rollback path.

## 10b) Operations (the boring parts that prevent headaches)

- **Backups**: automatic Home Assistant backups + a copy off the HA machine.
- **Updates**: scheduled maintenance window (e.g., quarterly) unless a security fix is urgent.
- **Monitoring**: alert on “device unavailable” for critical items (water valve controller, intercom, switches powering the network).
- **Documentation**: label circuits and keep a short “how to recover” checklist (restore backup, reboot order, VPN access).

## 10c) Build-time prerequisites (do these during construction)

The best way to keep smart-home cost and maintenance low is to invest early in **infrastructure** (pipes, ducts, conduits, wiring, space in cabinets). Smart devices can then be added gradually without opening walls.

### Mechanical / plumbing

- **Technical room layout**

  - Reserve wall space for: inverter + battery, ventilation unit + duct trunks, heat pump interfaces, manifolds/collectors, and the network rack.
  - Ensure service access (filters, valves, actuators) remains reachable after finishing.
- **Water shutoff valve placement**

  - Place the motorized main shutoff in an accessible, dry area close to the main entry and meter.
  - Add a **manual bypass / manual shutoff** you can use without power or apps.
  - Consider adding a small drip tray/floor drain concept in the tech room if feasible.
- **Leak-prone points: plan sensor locations**

  - Under sinks, dishwasher, washing machine, tech room near manifolds/filters, and any floor drain area.
  - Ensure there’s a practical place to mount sensors and route cables (for cable sensors like Water Guard).

### Heating system / manifolds / “collector pipes”

- **Manifold location and cabling path**

  - Put underfloor heating manifolds where wiring to actuators is easy and serviceable.
  - Provide a conduit/wiring route from each manifold area back to the electrical cabinet (for actuator power/control).
- **Zone design (keep it simple)**

  - Decide up front how many zones you really need; more zones = more actuators + more tuning.
  - Prefer “one room = one zone” only where it matters; otherwise group similar rooms.
- **Provision for future wired thermostats (optional but future-proof)**

  - Even if you start wireless, consider empty conduits from key rooms to the cabinet/manifold for future wired sensors/controls.

### Ventilation ducting

- **Access and serviceability**

  - Ensure filter access and condensate handling are easy.
  - Provide planned locations for any required external sensors/intakes and make sure cabling paths exist.
- **Network point for Zehnder gateway**

  - Provide a CAT6 run to where ComfoConnect PRO will be installed (often in/near technical room).

### Electrical (230V) prerequisites

- **Distribution board / DIN-rail space**

  - Reserve DIN-rail space for: Shelly Pro modules, power supplies (e.g., 24V DC), protection devices, and future expansion.
  - Keep a clear separation between mains and SELV/low-voltage sections.
- **Lighting circuits planned for automation**

  - Use deep wall boxes where modules must fit.
  - Ensure neutrals are present where needed (common practical blocker).
  - Label circuits and plan grouping (avoid very large LED groups on one channel if it can be split).
- **Critical power & UPS**

  - Provide a dedicated, labeled outlet/circuit for the rack.
  - Decide what must stay up on UPS (router/ONT, core switch, HA, intercom/doorbell).

### PV / battery / EV prerequisites

- **Inverter/battery placement and environment**

  - Wall space, ventilation, temperature considerations, and service access.
- **Metering and comms**

  - Ensure the Fronius Smart Meter location supports safe installation and reliable comms to the inverter/HA (cabling path).
- **EV charger**

  - Plan conduit/cable run, breaker sizing, and network connectivity (prefer Ethernet if possible).

### Low-voltage (LAN/CAT6) prerequisites

- **Star topology to the rack**

  - Run CAT6/6A from each room to the rack (don’t daisy-chain).
  - Plan at least:
    - 2 drops per living room / office / TV wall
    - 1–2 drops per bedroom
    - 1 drop per ceiling AP location per floor
    - 1 drop to intercom location
    - 1 drop to camera location(s)
    - 1 drop to inverter/energy equipment area (if they support Ethernet)
- **Conduits for change**

  - Add empty conduits from rack to attic/roof (PV, antenna, future sensors) and to outdoor points (gate, camera, weather station).
- **Door/window contacts**

  - If you want wired opening sensors (ABUS), plan cable routes during framing (doors, windows, garage/gate).

### Documentation deliverables during construction

- As-built drawings: circuit list, cable routes, manifold zoning, ventilation layout.
- Label everything: patch panel ports, switch ports, breaker map, valve/actuator IDs.

## 10d) Phased rollout (cash-flow friendly)

Goal: pay early for infrastructure (cheap to do while building), then add smart devices in stages.

### Stage 0 — “Infrastructure only” (during construction)

- All prerequisites in 10c completed.
- Rack location, power, ventilation, and cable termination plan decided.

### Stage 1 — “Essential & safety” (move-in ready, low maintenance)

- Network basics online (router + core switch + Wi‑Fi APs).
- Home Assistant running with backups enabled.
- Fire/CO system installed and working (independent of HA).
- Water leak detection in tech room + kitchen + main shutoff valve (with manual override).
- Basic lighting control where it adds value (entry, kitchen, hallway), keeping manual switch behavior intact.

### Stage 2 — “Comfort” (after move-in)

- Blinds on key rooms (bedrooms/living) with simple schedules + wind protection.
- Ventilation integration (monitoring + mode switching).
- Heating integration limited to mode/setpoint orchestration (avoid over-automation).

### Stage 3 — “Efficiency & optimization” (when budgets allow)

- PV + battery + EV charging automation (surplus-based, conservative thresholds).
- Add room-by-room air quality sensors if not done already.

### Stage 4 — “Nice-to-have” (only if you still want it)

- Presence-based automation beyond simple scenes (do carefully; tends to increase tuning).
- Additional cameras, more granular energy monitoring, extra sensors.

## 10e) Definition of Done (reliability targets)

- **Internet down**: lights work manually; heating/ventilation continue; doors/gate operate safely.
- **Home Assistant down**: nothing critical breaks; manual controls remain usable.
- **Power outage**: agreed safe behavior happens (water valve policy defined; UPS keeps core network up for target time).
- **Restore tested**: HA backup restore procedure verified once.

## 11) Open items checklist

- Router/firewall selection + VLAN design
- Home Assistant backup/restore approach
- UPS runtime target and what stays online
- Final counts per device (number of lighting circuits, blind motors, zones, sensors)
- Local vs cloud surveillance design and retention
