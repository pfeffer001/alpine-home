# Alpine Smart Home – Pricing (Scheffau, Austria)

Indicative street pricing for the DACH region (Germany, Austria, Switzerland). This sheet aligns with the current spec in [Alpine Smart Home.md](Alpine%20Smart%20Home.md).

## Assumptions (adjust to your house plan)

- Prices are **hardware only** (VAT varies by seller; assumed typically included).
- Counts that are not fixed in the spec are **TBD**; ranges assume a typical single-family house.
- Big-ticket items (heat pump / ventilation / PV / battery) often have significant planning + installation cost and may be bundled by installers.

## A) Phase-based totals (cash-flow friendly)

These totals are approximate and depend heavily on the number of lighting circuits, blind motors, and heating zones.

- **Stage 1 (Essential & safety for move-in):** ~**€4,500 – €8,500**
	- Core network + Home Assistant + Thread
	- Fire/CO system (independent)
	- Water leak + shutoff core
	- A few key lighting circuits (entry/kitchen/hall)

- **Full smart-home layer (excluding PV/HVAC big equipment):** ~**€9,000 – €18,000**
	- Expands lighting/blinds/sensors and completes access/security integrations

Big equipment (PV + battery + heat pump + ventilation) remains in the dedicated sections below.

---

## 1) Home Intelligence Central (Rack + HA)

*Estimated category total: **€1,100 – €1,550***

- Rack: Digitus 10" 9U wall mount: **€75 – €95**
- Patching: 2x Keystone panels + 24x slim patch cables: **€70 – €110**
- Power: Digitus 10" socket strip + Eaton 3S 700 DIN UPS: **€140 – €200**
- Switching: UniFi Switch Ultra + 210W Power Adapter (USW-Ultra-210W) + Switch Lite 8 PoE: **€300 – €450**
- HA server: Minix Z150-0dB (Intel N150): **€240 – €320**
- Thread adapter: Home Assistant Connect ZBT-2: **€45 – €60**

## 1a) Network gateway + Wi‑Fi (missing previously)

*Estimated category total: **€450 – €1,300***

- Router/firewall: **€0 – €400** (ISP router at €0; prosumer firewall if upgraded)
- Wi‑Fi access points (as per current spec):
	- 2x UniFi U7 Pro Max (in-house, PoE ceiling): **€350 – €650**
	- 1x UniFi U6 Mesh (garden, PoE outdoor): **€140 – €220**

## 2) Electric Energy Management

*Estimated category total: **€11,500 – €17,500+***

- Inverter: Fronius Gen24 (e.g., 10.0 Plus): **€2,100 – €2,500**
- Meter: Fronius Smart Meter TS 65A-3: **€220 – €250**
- Battery: Fronius Reserva / BYD Battery-Box (approx. 11kWh): **€5,800 – €6,500**
- Solar panels: SunPower Maxeon 6/7 (approx. 15–20 panels): **€4,500 – €7,500**
- EV charger: Fronius Wattpilot Flex 22kW: **€950 – €1,100**

## 3) Heat & Air Quality Management

*Estimated category total: **€9,600 – €14,300***

- Heat pump: Buderus Logatherm WLW176i-5 AR TP70: **€5,500 – €6,500** (unit only)
- System controller: Buderus MX400 (required for buffer tank hydraulics): **€250 – €550** (often bundled by installer)
- Room controller: Buderus Logamatic RC100.2 (H): **€120 – €250**
- DHW tank: Buderus Logalux SH290: **€1,900 – €2,300**
- EMS gateway: BBQKees Gateway E32 V2: **€115 – €150**
- Heating relays (DIN): Shelly Pro 4PM: **€90 – €130**
- Thermal actuators (UFH zones, count depends on design): **€15 – €30 each** (TBD count)
- Indoor air quality: Eve Room (6x): **€450 – €750**
- Outdoor weather: Tempest Weather Station: **€310 – €380**

## 4) Ventilation (Zehnder System)

*Estimated category total: **€4,200 – €6,400***

- Unit: Zehnder ComfoAir Q: **€2,700 – €3,600**
- Geothermal/ground preheater: Zehnder ComfoFond-L Q: **€1,100 – €1,700**
- Connectivity: ComfoConnect PRO + Option Box V2: **€450 – €800**

## 5) Lighting automation (lighting only)

*Estimated category total: **€700 – €1,600***

- Shelly switching/dimming modules: **€18 – €35 per channel/device** (mix of Minis, Dimmer, etc.)
- Assumption: **~25–45** modules total (TBD by circuit design)

## 6) Blinds automation (missing previously)

*Estimated category total: **€250 – €900***

- Shelly 2PM Gen4 (per blind motor / per 2 channels depending on wiring): **€25 – €45 each**
- Assumption: **~6–20** controlled blind channels (TBD)

## 7) Safety: Water + leak detection (missing previously)

*Estimated category total: **€250 – €700***

- Pulse module for water meter (IZAR PULSE i): **€60 – €140**
- Monitoring node: Shelly Plus Uni (DIN housing): **€20 – €35**
- Main shutoff valve: U.S. Solid Motorized Ball Valve (24V DC, 1", stainless steel, 2-wire auto return): **€70 – €250**
- Valve control: Shelly Pro 2: **€70 – €110**
- Leak sensors: Eve Water Guard (2x): **€140 – €220**
- Leak sensors: Aqara Water Leak Sensor P2 (1–2x): **€20 – €60**

## 8) Fire & CO safety (missing previously)

*Estimated category total: **€500 – €900***

- Bosch Smart Home Controller II: **€90 – €140**
- Bosch Smoke Alarm II (5x): **€300 – €550**
- Bosch CO alarms (2x): **€180 – €320**

## 9) Security, access & gate

*Estimated category total: **€2,700 – €3,900***

- Intercom: 2N IP Verso 2.0 (standard config): **€1,700 – €2,000**
- CCTV camera: AXIS M3116-LVE: **€320 – €450**
- Lock cylinder: 2N Fortis: **€380 – €550**
- Door/window sensor (Thread): Eve Door & Window (count TBD): **€35 – €60 each**
- Presence sensors: Aqara Presence Sensor FP2 (count TBD): **€60 – €95 each**
- Wired openings: ABUS FU7350W (count TBD): **TBD**
- Gate control: Shelly Pro 2 + sensors/relays + magnetic switches: **€140 – €220**

## Optional / often forgotten line items

- Network storage / NVR (if recording locally): **€200 – €800**
- Cloud video subscription (if used): **€0 – €200/year+**
- Extra PoE budget (more cameras/APs): **€0 – €400**

---

## Important pricing notes

- **Installation / commissioning**: Professional installation for HVAC/PV and electrical work can add a significant amount (often **30%–50%+** of hardware), and varies strongly by scope.
- **Cabling & conduits**: CAT6 runs, conduits, patching labor, electrical cabinet sizing, and ventilation ducting are typically part of the build contract and can dwarf “smart device” costs.
- **Subsidies**: PV/heat-pump/energy storage subsidies may apply; amounts and eligibility change over time.