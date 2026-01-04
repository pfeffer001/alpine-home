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
- Rack: [Digitus 10" 9U Wall Mount](https://de.assmann.shop/en/Network-and-Server-Cabinets/Network-Cabinets-Wall-Mounting/10-Wall-Mounting-Cabinets/Wall-mounted-enclosure-SOHO-Pro-254-mm-10-315x300-mm-WxD-var.html)
- Patch: 2x [Digitus 10" 12-Port Keystone Panel](https://de.assmann.shop/en/Data-Network-Technology/Copper-Cabling/Patch-Panels/Modular-Patch-Panel-12-port-var.html?listtype=search&searchparam=10%22%2012-Port%20Keystone%20Panel)
- Patch cables: 24x 0.25m slim-line
- Power strip: [Digitus 10" Socket Strip (4x Schuko)](https://www.digitus.info/en/products/power-and-computer-accessories/power-distribution/power-strips/dn-95461/)
- UPS: [Eaton 3S 700 DIN](https://www.eaton.com/de/en-gb/skuPage.3S700DIN.html) (external placement)

**Network**
- PoE Switch A: [Ubiquiti UniFi Switch Ultra](https://store.ui.com/us/en/pro/category/all-switching/products/usw-ultra) + with 210W Power Adapter (USW-Ultra-210W)
- PoE Switch B: [Ubiquiti UniFi Switch Lite 8 PoE](https://store.ui.com/us/en/pro/category/all-switching/products/usw-lite-8-poe)
- Internet router/firewall (10" rack): UniFi Cloud Gateway on shelf (ISP modem/ONT in bridge mode)
  - Preferred (future multi‑gig): [Cloud Gateway Max](https://store.ui.com/us/en/pro/category/all-unifi-cloud-gateways/products/ucg-max)
  - Budget (1 Gbit-class): [Cloud Gateway Ultra](https://store.ui.com/us/en/pro/category/all-unifi-cloud-gateways/products/ucg-ultra)

**Wi‑Fi**

- In-house APs: 2x [UniFi U7 Pro Max](https://store.ui.com/us/en/pro/category/all-wifi/products/u7-pro-max) (PoE, ceiling-mounted)
- Garden AP: 1x [UniFi U6 Mesh](https://store.ui.com/us/en/pro/category/all-wifi/products/u6-mesh) (PoE, outdoor-rated; install under eaves facing garden)
- Provisioning principle: plan at least 1 ceiling AP per floor (CAT6 run to rack + PoE); adjust count after a site survey

**Compute & radios**

- Home Assistant server: [Minix Z150-0dB](https://minix.us/z150-0db) (Intel N150, 16GB RAM, 512GB SSD)
- Thread adapter: [Home Assistant Connect ZBT-2](https://www.home-assistant.io/connect/zbt-2) (USB extension)

**Open design items**

- VLAN / segmentation plan (Trusted / IoT / Cameras / Guest)
- Remote access approach (prefer VPN vs port forwards)
- UPS runtime target + what is on UPS (router/ONT, switches, HA, intercom)

## 5) Electric energy management

- Hybrid inverter: [Fronius Gen24](https://www.fronius.com/en/solar-energy/installers-partners/products-solutions/inverters/fronius-primo-gen24-plus)
- Power meter: [Fronius Smart Meter TS 65A-3](https://www.fronius.com/en/solar-energy/installers-partners/products-solutions/monitoring-digital-tools/fronius-smart-meter)
- Battery: [Fronius Reserva](https://www.fronius.com/en/solar-energy/installers-partners/products-solutions/storage-systems)
- Solar panels: [SunPower Maxeon](https://sunpower.maxeon.com/int/solar-panels-sunpower-maxeon)
- EV charger: [Fronius Wattpilot Flex Home 22kW](https://www.fronius.com/en/solar-energy/installers-partners/products-solutions/emobility/fronius-wattpilot)

**Automation intents (examples)**

- PV surplus → EV charging / DHW heating priority.
- Peak shaving using battery (policy depends on tariffs).

## 6) Heat energy & air quality

**Heating / DHW**

- Heat pump: [Buderus Logatherm WLW176i-5 AR TP70](https://www.buderus.de/de/produkte/catalogue/buderus-produkte/waermeerzeuger/waermepumpen/logatherm-wlw176i-ar)
- System Controller: Buderus MX400 (required for buffer tank hydraulics)
- Controller: [Buderus Logamatic RC100.2 (H)](https://www.buderus.de/de/produkte/catalogue/buderus-produkte/regelsysteme/system-bediengeraete/logamatic-rc100h)
- External DHW tank: [Buderus Logalux SH290](https://www.buderus.de/de/produkte/catalogue/buderus-produkte/speicher/warmwasserspeicher/logalux-sh)
- Gateway: [EMS-ESP via BBQKees Gateway](https://bbqkees-electronics.nl/product/gateway-e32-v2-9-wired-for-ems-bus/) (for HA communication)
- Heating actuator relays: [Shelly Pro 4PM](https://www.shelly.com/products/shelly-pro-4pm)
- Thermal actuators: [Möhlenhoff Alpha 5](https://www.moehlenhoff.de/en/products-solutions/actuators/alpha-5/) (230V NC)

*Notes:*
- *The MX400 is required by the installer for the specified hydraulic design (with buffer tank). It is not replaced by the EMS-ESP gateway.*
- *The EMS-ESP gateway allows Home Assistant to monitor the system and orchestrate setpoints/modes.*
- *A physical room controller (e.g., Buderus RC100) is still recommended for commissioning and as a reliable manual fallback.*
- ***Hierarchy of Control:** To prevent conflicts, the Buderus controller (RC100) will own the primary heating schedule. Home Assistant will act as a supervisor, sending high-level commands for exceptions (e.g., "Away Mode," "Vacation Mode," temporary boosts). Manual changes on the physical controller will be respected by Home Assistant and not overridden automatically.*

**Buderus support / warranty concern when connecting EMS-ESP (practical guidance)**

- **Reality check:** EMS-ESP is a third‑party device. Some installers/manufacturers may refuse to troubleshoot issues while third‑party bus devices are attached, or may claim it’s “unsupported”. Whether this affects warranty/support is ultimately a policy/contract question (ask your installer in writing).
- **Risk reduction approach (recommended):**
  - Install EMS-ESP as a **non-invasive, reversible add-on** on the EMS/EMS+ bus (no cutting proprietary harnesses; no permanent modifications).
  - Keep **Buderus-native controls** (RC100/MX400) as the primary operating interface so the system remains serviceable without HA.
  - Start with **read-only monitoring** in HA, then enable only coarse commands (mode/preset/setpoint) once stability is proven.
  - Document a “service mode” step: **unplug EMS-ESP** before a service visit if the installer requests a fully vendor-standard setup.
- **What to confirm with the installer:**
  - Where the EMS/EMS+ bus may be tapped safely.
  - That adding a bus listener/controller doesn’t violate their commissioning/support conditions.
  - That the system remains within spec with HA disconnected (it should).

**How Home Assistant overrides the Buderus schedule (exception handling)**

- **Principle:** HA does not rewrite the RC100 schedule. Instead it activates a vendor-supported *override mode* (preferably **Holiday/Vacation**) with an explicit end time, then returns control to the normal schedule.
- **Typical precedence:** Safety bounds (freeze protection / max temp) > explicit HA override (time-bounded) > manual local override on RC100 > normal RC100 schedule.

**Example voice command:** “Leaving for 2 weeks, turn the heating to eco mode.”

- Assist/voice triggers a HA script that:
  - Sets `heating_override = vacation` and `heating_override_until = now + 14 days`.
  - Sends one high-level command via EMS-ESP:
    - Best case: set Buderus to **Holiday/Vacation** until the end date (controller then ignores the normal schedule during that period).
    - Fallback: set the heating circuit preset/setpoint to **Eco** (or a lower target room temp) and keep it there until the end date.
- A second automation watches the `heating_override_until` timestamp and, when reached:
  - Clears the override flag.
  - Restores the previous operating mode (e.g., `auto` / scheduled), returning control to the RC100 schedule.

**Short-term override example:** “Set heating to 21°C for the next 3 hours.”

- HA treats this as a **temporary override** (`heating_override_type = boost` or `temporary_setpoint`) with `heating_override_until = now + 3 hours`.
- HA sends a single coarse command via EMS-ESP (whichever is supported reliably in your setup):
  - Set heating circuit to **manual/temporary** and apply a target room temperature (e.g., 21°C), or
  - Switch to **Comfort** preset (if that effectively yields the desired temp), optionally adjusting the comfort setpoint.
- When the timer expires, HA returns the circuit to **Auto/Scheduled** so the normal RC100 schedule resumes.

**Implementation detail (HA-side state so it’s explainable)**

- Track override state explicitly in HA (e.g., `input_boolean.heating_override_active` + `input_datetime.heating_override_until` + `select.heating_override_type` with values like `none|eco|vacation|boost`).
- Keep the commands coarse: change only preset/mode/setpoint, not curves or frequent toggles.
- If someone changes the RC100 manually during an active override, HA should either:
  - Respect it and cancel the override (simplest), or
  - Notify “Heating override was changed locally; keeping manual control.”

**Can you control the Buderus heating schedule from the HA dashboard?**

- **Usually not in a “true edit the RC100 schedule” way.** Most Buderus controllers don’t expose full schedule CRUD (create/read/update/delete) as clean entities that HA can edit. EMS-ESP typically gives you reliable monitoring + setpoints/modes/presets, but not a rich schedule editor UI.
- **Recommended (low maintenance):** Keep the schedule in RC100 and put *override controls* on the HA dashboard:
  - `Home/Away/Night` (mode)
  - `Eco/Comfort` (preset)
  - `Vacation until <date>` (time-bounded override)
  - Optional: `Boost 1h` (temporary)
- **If you insist on HA being the scheduler:** You can build the schedule in HA (Schedule helper / automations) and have HA set the Buderus preset/setpoint at those times.
  - Tradeoff: more HA dependency and more “automation tuning” (against the guiding principles).
  - Safer approach: set RC100 to a simple constant baseline and let HA do only predictable, coarse changes.

**Indoor / outdoor sensing**

- Indoor air quality: 6x [Eve Room](https://www.evehome.com/en/eve-room)
- Outdoor weather: [Tempest Weather Station](https://shop.weatherflow.com/products/tempest)

**Blinds**

- Blinds control: [Shelly 2PM Gen4](https://www.shelly.com/products/shelly-2pm-gen4)

### Ventilation

- Ventilation unit: [Zehnder ComfoAir Q](https://www.zehnder-systems.de/produkte-und-systeme/komfortable-wohnraumbelueftung/zehnder-comfoair-q)
- Gateway: [Zehnder ComfoConnect LAN C](https://www.zehnder-systems.de/produkte-und-systeme/komfortable-wohnraumbelueftung/connectivity/zehnder-comfoconnect-lan-c)
- Air heat exchanger: [Zehnder ComfoFond-L Q](https://www.zehnder-systems.de/produkte-und-systeme/komfortable-wohnraumbelueftung/erdwaermetauscher/zehnder-comfofond-l-q)
- Zehnder Option Box V2

**Automation intents (examples)**

- CO₂/humidity-driven ventilation setpoints (bedrooms/night focus).
- Wind/rain-aware blind protection.

## 7) Lighting (and only lighting)

### Switching

- Light switch (8A): [Shelly 1 Mini Gen4](https://www.shelly.com/products/shelly-1-mini-gen4)
- Light switch + power monitoring (8A): [Shelly 1PM Mini Gen4](https://www.shelly.com/products/shelly-1pm-mini-gen4)
- Dry-contact relay (for control signals): [Shelly 1 Gen4](https://www.shelly.com/products/shelly-1-gen4)

### Dimming

- Light dimmer: [Shelly Dimmer Gen4](https://www.shelly.com/products/shelly-dimmer-gen4)

*Notes to validate with electrician / fixture selection*

- *Confirm **neutral availability** and **mounting space** behind switches/junction boxes.*
- *Validate LED **dimmer compatibility** (driver type, minimum load, flicker performance).*
- *Validate LED **inrush current** vs relay/dimmer limits for each circuit group.*

## 8) Safety

### Water pipelines (consumption monitor + shutoff)

- Main meter: [Diehl Auriga](https://www.diehl.com/metering/en/products-services/products/water/auriga/)
- Pulse module: [IZAR PULSE i](https://www.diehl.com/metering/en/products-services/products/system-components/izar-pulse-i/) (inductive clip-on)
- Monitoring node: [Shelly Plus Uni](https://www.shelly.com/products/shelly-plus-uni) (DIN-rail housing)
- Shut-off valve: [U.S. Solid Motorized Ball Valve](https://www.ussolid.com/motorized-ball-valves.html) (24V DC, 1" Stainless Steel, 2-Wire Auto Return)
- Valve control: [Shelly Pro 2](https://www.shelly.com/products/shelly-pro-2) (dedicated or spare channel)

**Key decisions to document**
- Shut-off valve: U.S. Solid Motorized Ball Valve (fail-open) - to be dicussed with plumber!!
- Valve “safe state” during power loss (fail-open vs fail-closed).
- Manual override / bypass plan.

### Water leakage sensors

- Tech room: 1x [Eve Water Guard](https://www.evehome.com/en/eve-water-guard) (Matter over Thread, 2m cable)
- Kitchen: 1x [Eve Water Guard](https://www.evehome.com/en/eve-water-guard) (Matter over Thread, 2m cable)
- Bathroom: 1–2x [Aqara Water Leak Sensor P2](https://www.aqara.com/us/product/water-leak-sensor-p2/) (Matter over Thread)

### Fire safety (insurance compliant)

- Bridge: [Bosch Smart Home Controller II](https://www.bosch-smarthome.com/de/de/produkte/geraete/smart-home-controller-2/) (Ethernet)
- Smoke: 5x [Bosch Smoke Alarm II](https://www.bosch-smarthome.com/de/de/produkte/geraete/rauchwarnmelder-2/) (interconnected, VdS)
- CO: 1x [Bosch Carbon Monoxide Alarm](https://www.bosch-smarthome.com/de/de/produkte/geraete/kohlenmonoxid-melder/) (living room / fireplace)
- CO: 1x [Bosch Carbon Monoxide Alarm](https://www.bosch-smarthome.com/de/de/produkte/geraete/kohlenmonoxid-melder/) (technical room)

## 9) Security

### Surveillance

- Garden camera: [AXIS M3116-LVE](https://www.axis.com/products/axis-m3116-lve)

**Open decisions**

- Cloud vs local recording/NVR and retention policy.
- Privacy boundaries (where cameras are allowed, masking, access controls).

### Entrance

- Intercom: [2N IP Verso 2.0](https://www.2n.com/en_GB/products/intercoms/2n-ip-verso-2-0)
- Smart lock cylinder: [2N Fortis Cylinder](https://www.2n.com/en_GB/products/access-control-readers/2n-fortis-cylinder)
- Open window sensor (Thread): [Eve Door & Window](https://www.evehome.com/en/eve-door-window)
- Open windows/doors (wired): [ABUS FU7350W](https://www.abus.com/de/Sicherheit-Zuhause/Alarmanlagen/Draht-Alarmanlagen/Oeffnungsmelder/Magnetkontakt-fuer-Aufschraubmontage-FU7350W)
- Presence sensors: 6x [Aqara Presence Sensor FP2](https://www.aqara.com/us/product/presence-sensor-fp2/) (verify: typically Wi‑Fi)

### Gate

- Gate control: [Shelly Pro 2](https://www.shelly.com/products/shelly-pro-2)
- Gate sensors: 2x 24V DC coupling relays
- Magnetic switch: 2x [Seco-Larm SM-226L-3Q](https://www.seco-larm.com/product/sm-226l-3q/)

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

- UniFi gateway model selection + VLAN design
- Home Assistant backup/restore approach
- UPS runtime target and what stays online
- Final counts per device (number of lighting circuits, blind motors, zones, sensors)
- Local vs cloud surveillance design and retention
