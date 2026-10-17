# Shopping List — Single-Pump Drip Watering (4x4 + 5x5)

Prices checked **Sep 2026**. USD. Re-check the cart before buying.

## How this build works

One existing **120 V mains submersible pump** in the rolling garbage can feeds **all five 20-gal pots at once** (1 in the 4x4, 4 in the 5x5). Identical drippers per pot → both tents use the **same run time**. No valves, no manifold, no closet ESP32, no MOSFET/relay, no 12 V supply.

- Pump plugs into a **Shelly Plug US Gen4** (local auto-off set to 15 min + power metering).
- Shelly plugs into a **GFCI adapter** (unless that circuit is already GFCI).
- Capacitive moisture probes on the **existing** `tent-4x4-sensor` / `tent-5x5-sensor` boards (GPIO34; pins 21/22 stay on the BME280).
- HA turns the Shelly on when a probe reads dry (e.g. either tent dry, or both), only during lights-on if you keep that rule.
- **Siphon break instead of a check valve:** drill a small (~1/16") air-bleed hole at the high point of the outlet tubing *inside* the can, above the water line. When the pump stops, air enters and breaks the siphon so pots don’t keep draining the reservoir. Free.

Target: about **$100** for the required row.

---

## Required

| Item | Qty | Approx price | Link | Notes |
|------|-----|--------------|------|-------|
| Shelly Plug US Gen4 | 1 | **$24.99** | [us.shelly.com — Plug US Gen4](https://us.shelly.com/products/shelly-plug-us-gen4-black) (white same price) | Power metering + **local countdown auto-off**. Set auto-off to 900 s (15 min) on the plug; HA's pulses are capped at 12 min. Rated 15 A / 1800 W — confirm above your pump’s draw. All 4 Kasa KP125M plugs are already in use. |
| Capacitive soil moisture sensors | 2 used | **$9.49** for 5-pack | [Amazon — diymore 5pcs](https://www.amazon.com/dp/B07RZBKJM5) | One probe per tent on the existing ESP32. Coat the exposed PCB edge. Calibrate dry/wet. Spares left in the pack. |
| Drip kit with adjustable drippers | 1–2 kits | **$14.99** (1 kit) · **~$30** if you buy two | [Amazon — HIRALIY 50FT](https://www.amazon.com/HIRALIY-Watering-Automatic-Irrigation-Connector/dp/B0CS3M7783) | 50 ft 1/4" tubing, 5-way splitter, **12 adjustable drippers**, stakes, end plugs. For 5 pots: **2–3 drippers per pot** fits one kit; **4 per pot (~20)** → buy two kits, or one kit + extra emitters (Orbit pack below). Prefer adjustable / non-PC emitters for a low-pressure pump. |
| Extra emitters / fittings (if needed) | 1 | **$13.99** | [Amazon — Orbit 69500 92-pc](https://www.amazon.com/dp/B08KJPHK4C) | Optional add-on: more 1 GPH drippers, tees, plugs, stakes. No long tubing in this pack. |
| Inline filter, 150 mesh | 1 | **$2.00** (sale) / **$4.29** list | [Orbit — 3/4" FHT×MHT 150-mesh](https://orbitirrigate.com/product/3-4-in-fht-x-mht-hose-faucet-drip-filter/) | Hose-thread filter. Put it after the pump adapter, before the drip tubing. Clean often; set the pump on a brick so it doesn’t suck sediment. |
| Pump outlet → drip tubing adapter + hose clamps | 1 set | **price not verified** (~$5–15 class) | Amazon / HD: search “hose barb adapter” matching your pump outlet (often 1/2" or 3/4" barb / GHT) | Read the pump label for outlet size. Leave slack — the can rolls. Common path: pump barb → short hose → GHT filter → 1/4" or 1/2" drip adapter. |
| Plug-in GFCI adapter | 1 | **$17.99** | [Amazon — ELEGRP 15 A GFCI adapter](https://www.amazon.com/ELEGRP-GFCI-Adapter/dp/B09CKHWH26) | Pump → Shelly → GFCI → wall. Skip only if that closet circuit is already GFCI protected. Also seen ~$20 at Home Depot (Husky). |

### Required-only total

| | Low | High |
|--|-----|------|
| **Required** | **~$75** | **~$105** |

Low = one HIRALIY kit (2–3 drippers/pot) + Orbit filter on sale + cheap adapter. High = two drip kits or kit+Orbit parts + adapter at the top of the rough range. Stays near the **~$100** target.

---

## Recommended add-on — reservoir low (dry-run) protection

Without a float switch, protect the pump by: (1) tracking gallons in HA (subtract estimated gallons per cycle, reset when you refill), and (2) watching Shelly power — if the pump runs with near-zero watts, it’s dry or jammed; auto-off still caps runtime.

A float switch is more reliable.

| Item | Qty | Approx price | Link | Notes |
|------|-----|--------------|------|-------|
| Vertical float switch | 1 | **~$8–12** for common 2-packs (Amico-style listing seen at **$9.99**; confirm live) | Amazon search: “vertical float switch 2pcs” / [example ASIN B00FHADE42](https://www.amazon.com/dp/B00FHADE42) | Mount a few inches above the pump intake. Wire so the switch is **closed when water is above it** (low water or cut wire both read “low”). Connect to a free GPIO + GND on the **nearest** existing tent ESP32 (internal pull-up). |
| 2-conductor wire to that ESP32 | ~25–50 ft | **$26.99** for 50 ft 18/2 | [Amazon — Eastwire 18/2 50ft](https://www.amazon.com/dp/B0CQD57GN4) | Only if the can is far from a tent board. For a short run, leftover Dupont or any thin 2-conductor wire is fine and keeps you under $100. Signal is dry contact — no 12 V on this pair. |

### Required + float switch

| | Approx |
|--|--------|
| Required + float, **reuse leftover wire** | **~$85–$115** |
| Required + float + new 50 ft 18/2 | **~$110–$140** |

---

## Optional (not in the ~$100 target)

| Item | Qty | Approx price | Link | Notes |
|------|-----|--------------|------|-------|
| Pot scale (4× 50 kg half-bridge + HX711) | 1–2 kits | **$7.88 each** | [Amazon — Wishiot](https://www.amazon.com/dp/B0CWLHXHMD) | One representative pot per tent. HX711 on GPIO32/33 of that tent’s existing board. Add plywood plates (DIY). |
| Shelly Flood S Gen4 leak sensor | 1 (or 3) | **$27.99 each** | [us.shelly.com — Flood S Gen4](https://us.shelly.com/products/shelly-flood-s-gen4) | Wi-Fi / Matter; no Zigbee hub. One under the can/manifold area is the best first buy; three if you want each tent + reservoir. |

### With optionals

| | Approx |
|--|--------|
| Required + float (leftover wire) + 1 pot scale + 1 Flood | **~$120–$155** |
| Required + float + 2 pot scales + 3 Floods | **~$190–$230** |

---

## Not needed (and why)

| Skipped | Why |
|---------|-----|
| Check valve | Siphon-break hole at the high point inside the can does the same job for free. |
| Solenoid / ball valves | All pots water together; same run time. |
| Closet ESP32 | Moisture on existing tent boards; pump on Shelly. |
| MOSFET / relay module | No 12 V valves or DC pumps. |
| 12 V PSU / buck / diodes | Nothing low-voltage powered except the ESP32s you already have. |
| Flow meter | Time the run into a jug once; Shelly power confirms the pump started. |
| Manifold / separate tent zones | Same emitters → same duration for both tents. |

---

## Totals summary

| Build | Approx range |
|-------|----------------|
| **Required only** | **~$75–$105** |
| **Required + float (reuse wire)** | **~$85–$115** |
| **Required + float + new 18/2** | **~$110–$140** |
| **+ pot scale(s) + leak sensor(s)** | see optional table |

---

## Stuff you might already have

- **Garden drip leftovers** — 1/4" tubing, adjustable drippers, tees, stakes, end caps, hose clamps. Buy only what’s missing.
- **Leftover Dupont F-F jumpers** — moisture probes and a short float-switch run.
- **Any spare 2-conductor wire** — doorbell / thermostat / speaker wire for the float.
- **Hose clamps and barb fittings** from prior projects.
- **Existing pump + rolling reservoir** — already owned; note outlet size before ordering the adapter.
- **Spare laptop HA** — no new hub.
- **All 4 Kasa KP125M plugs are in use** — don’t reassign one to the pump; buy the Shelly for local auto-off + metering.

---

## Setup notes (short)

1. Plumb: pump → adapter → filter → drip lines → 2–4 adjustable drippers per 20-gal pot. Keep both tent layouts similar.
2. Drill the siphon-break hole at the tubing high point inside the can.
3. Shelly on the pump; set local auto-off to 900 s (15 min); note the watts when the pump runs.
4. Flash/add moisture sensors on the two tent boards; calibrate.
5. Measure one pot's dripper flow into a bucket and enter it as **Dripper flow per pot** in HA; HA works out the run time for 1.5 gal per pot (see WATERING.md).
6. HA (`packages/watering.yaml`): waters when dry during lights-on in 3 pulses, 12 h cooldown from `last_watered_*`, tracks reservoir gallons, stops if Shelly watts look wrong or the float says low.

---

## Why not the other options (brief)

- **Cheap plastic 12 V solenoids (~$5–10):** Real listings (e.g. Ximimark 2-pack at $9.99) say **0.02–0.8 MPa** and “diaphragm (operated by Servo)” — that’s **~3 psi minimum**, pilot/servo style. On a small submersible they often click but don’t flow. Skip them for this pump.
- **True 0-psi brass solenoids or motorized ball valves:** Work, but add ~$40–80+ per valve plus a closet board and 12 V — blows the budget when all pots share one run time.
- **One 12 V pump per tent:** Possible (~$23 brushless 800 L/h / ~4 m head) but needs 12 V PSU + switching per pump, more plumbing, and siphon care — more cost and failure points than one Shelly on the pump you already own.
- **Gravity feed:** A full 32-gal can of water is ~267 lb (8.34 lb/gal) plus the can — awkward to elevate in a closet, and you still need 0-psi or motorized valves if you zone. Not worth it here.
- **Cheaper plugs (Sonoff S31 ~$11, Athom ESPHome ~$10):** Fine if flashed for local HA + on-device auto-off in ESPHome. You already chose Shelly Gen4 for metering + local countdown without opening a case.

---

## Unverified / weak pins

- Exact pump→drip barb/GHT adapter SKU (depends on your pump outlet; ~$5–15 class)
- Live Amico/float-switch 2-pack price (search showed ~$9.99; confirm before cart)
- Whether one HIRALIY kit’s 12 drippers are enough for your emitter layout (buy a second kit or Orbit 69500 if you want 4 per pot)
