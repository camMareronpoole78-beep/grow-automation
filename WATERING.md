# Single-pump drip watering: 4x4 + 5x5 (five 20-gallon pots)

The pump you already have sits in the rolling garbage-can reservoir and waters
**all five pots at once**: one in the 4x4 and four in the 5x5. There are no valves,
no manifold, and no extra ESP32. A Shelly Plug US Gen4 switches the pump. The
moisture probes on your existing 4x4 and 5x5 tent boards tell Home Assistant when
to water. Watering only happens while the lights are on, currently 7 PM to 7 AM.
The Shelly's own auto-off timer is the safety backstop if Home Assistant or Wi-Fi
drops out mid-run.

Parts and prices are in `SHOPPING-LIST-WATERING.md`.

## What changed from the last version

- **Removed:** both solenoid valves, the closet manifold, the closet watering-controller ESP32 (`watering-controller.yaml` is deleted), the MOSFET/relay board, the 12 V supply and buck converter, the diodes, the check valve, and the flow meter.
- **Pump control:** the pump runs from one Shelly plug (`switch.reservoir_pump`). Home Assistant turns it on for each pulse, and the plug's auto-off (set to 15 min) cuts it if anything hangs. A 13-minute runaway automation in HA backs that up.
- **Sizing:** one "gallons per pot" setting now applies to every pot, because all five share one pump and one run time. The defaults are 1.5 gal per pot, three pulses, and 10-minute soaks.
- **Trigger:** pick **Either tent is dry** (the default) or **Both tents are dry**. Each tent keeps its own dryness threshold. The rule also re-checks at lights-on, and there's a 12-hour cooldown based on your watered log.
- **Reservoir:** without a float-switch board, Home Assistant keeps a running estimate of the gallons in the can. It subtracts each pulse, and you press **Reservoir refilled** after filling. It won't start a watering that would take the can below the reserve. A float switch wired to one existing tent board is optional (`esphome/reservoir-float.yaml`).
- **Pump health:** the pump's power draw is checked 8 seconds into every pulse. No power means a tripped GFCI or a dead pump. Power below the `Pump minimum watts` you set can mean it's running dry or the intake is clogged. Either one stops the watering and alerts you.
- **Logging:** every finished watering presses both `watered_4x4` and `watered_5x5`, so your log and 9 AM reminders keep working.
- **ESPHome:** `tent-4x4.yaml` and `tent-5x5.yaml` include `tent-watering-sensors.yaml`, which is the moisture probe on GPIO34. The pot scale is now a commented-out option. The lung room board is unchanged.

## Timing (defaults: 1.5 gal per pot, 8 GPH of drippers per pot)

All pots water together, so the pump-on time depends only on gallons per pot ÷ dripper flow per pot.

| | Value |
|---|---|
| Water per pot | 1.5 gal |
| 4x4 (1 pot) | 1.5 gal |
| 5x5 (4 pots) | 6 gal |
| Reservoir used per watering | 7.5 gal |
| Pump-on time | 11 min 15 s total, as 3 pulses of 3 min 45 s |
| Soak between pulses | 10 min |
| Whole watering, start to finish | about 31 min |
| Flow the pump must supply | 5 pots × 8 GPH = 40 GPH (about 0.7 gal/min) |

At the 2-gallon maximum per pot, a watering uses 10 gallons, with 3 pulses of 5 minutes each. If your drippers only add up to 4 GPH per pot, every time doubles. Home Assistant refuses pulses longer than 12 minutes, so the Shelly's 15-minute auto-off never cuts off a normal pulse. If you ever hit that limit, add a pulse.

## Plumbing

The water path is pump, then a short hose, then the 150-mesh hose-thread filter, then the drip kit's 5-way splitter. From the splitter, **one 1/4" line runs to each pot**: one to the 4x4 pot and four to the 5x5 pots. Each line ends in that pot's drippers (2 to 4 adjustable drippers per pot). Giving every pot its own line from one splitter keeps the flow even. With the 12 drippers in one HIRALIY kit, you can do 2 per pot. For 4 per pot, get a second kit or the Orbit emitter pack.

- **Same drippers in every pot.** The whole design assumes every pot gets the same flow. Use adjustable drippers so you can even them out. The 4x4's single pot on a short line tends to get a little more than the far 5x5 pots. Pressure-compensating drippers would even this out automatically, but most need about 10 psi, which a small submersible pump may not reach at tent height.
- **Siphon break instead of a check valve.** When the pump stops, water can keep siphoning out of the can if any dripper sits below the can's water line. Drill a 1/16" hole in the delivery tube at its highest point, just inside the rim of the can, so it drips back into the can. When the pump stops, air gets in through the hole and the siphon breaks. It costs you a small trickle back into the can while the pump runs, and that's all.
- **Filter.** Put a 150 to 200 mesh inline filter right after the pump, and set the pump on a brick so it doesn't pick up sediment. Don't pump teas or liquid feeds through the drippers, because they clog. Feed by hand as you do now.
- **Pump head.** Check the max head on your pump's label. It should be at least twice the height from the can's low water level to the highest dripper.
- **Hose top-offs.** If you fill the can from a household hose, keep the hose end above the water. Never leave it sitting in the can.
- **Wheels.** Leave slack in the pump's tubing and power cord, and give the cord a drip loop below the outlet.

## Wiring

- **Pump:** plug the pump into the Shelly Plug US Gen4, the Shelly into a GFCI adapter (skip the adapter if the outlet is already GFCI), and that into the wall. You don't do any mains wiring.
- **Moisture probe, one per tent board:** probe VCC to 3V3, GND to GND, and AOUT to GPIO34. The BME280 stays on GPIO21/22. In the 5x5, put the probe in one representative pot.
- **Optional float switch:** connect it between GPIO25 and GND on whichever tent board is closest to the can, using a 2-conductor wire run. Uncomment the `reservoir_float` line in that board's YAML only. Mount it a few inches above the pump intake, oriented so it's **closed while water is above it**.

## Setup

1. **Shelly.** Add the plug in the Shelly app and join it to your Wi-Fi. Set **Auto off** to 900 seconds. In HA, add it through Settings > Devices & Services > Add Integration > Shelly (local, no cloud needed), then rename it "Reservoir Pump" so the entities become `switch.reservoir_pump` and `sensor.reservoir_pump_power`.
2. **Probes.** Flash `tent-4x4.yaml` and `tent-5x5.yaml` over the air, the same way you normally update them. To calibrate a probe, temporarily remove its `calibrate_linear` filter. Note the voltage in dry mix and then in a glass of water, put both numbers into `calibrate_linear`, and reflash.
3. **Package.** Copy `homeassistant/packages/watering.yaml` into `config/packages/`, and add this once to `configuration.yaml`:
   ```yaml
   homeassistant:
     packages: !include_dir_named packages
   ```
   Then restart HA.
4. **Measure your dripper flow.** Put one pot's drippers in a bucket and run the pump for 5 minutes (turn on `switch.reservoir_pump` by hand). Multiply the gallons collected by 12 to get GPH per pot, and set **Dripper flow per pot** to that number. Check a 5x5 pot too, to confirm the far pots flow about the same.
5. **Reservoir.** Set **Reservoir usable capacity** to what your can holds when full, fill it, and press **Reservoir refilled**.
6. **Pump watts (optional).** During a watering, note `sensor.reservoir_pump_power`. Set **Pump minimum watts** a bit below that number, and delete its `initial:` line so the setting survives restarts.
7. **Thresholds.** Watch the moisture readings for a week of hand watering. Set **4x4 dry below** and **5x5 dry below** to the reading where you'd normally water, pick **Water when**, and turn on **Auto-watering enabled**.
8. **Test run.** Run **Water all pots** from the dashboard during lights-on and watch the first pulse.

## Limits to know

- Both tents always get the same amount per pot. If one tent needs noticeably more or less, adjust that tent's drippers (for example, one more or one fewer dripper per pot) instead of changing the setting.
- The reservoir level is an estimate, and it drifts if the flow or the siphon-break trickle differs from the settings. Re-measure the dripper flow now and then, and keep pressing Refilled. The float switch is the backup if you want one.
- The dry-run check depends on your pump drawing noticeably less power when it runs dry. Many do, but some don't.
- `esphome config` validates the two tent boards (with and without the float switch) and the unchanged lung room board. Nothing has been tested on real hardware.
