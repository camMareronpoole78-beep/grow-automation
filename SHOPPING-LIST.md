# Shopping List

Listings pulled Aug 2026 — prices fluctuate on all of these, check
the cart total before buying. Everything here is one-time hardware, no
subscriptions.

## Deferred — running on a spare laptop instead for now

You can start on a spare laptop (free VirtualBox VM, see README.md)
to cut upfront cost, and buy the Pi once the rest of the build is
proven out. Buy-now total below excludes the hub. **Buy this whenever
ready to move off the laptop** — see README.md's "Moving to the Pi later"
section for the zero-rework migration path (one backup/restore, nothing
gets rebuilt).

## The hub (buy once, covers all 3 tents forever — deferred for now)

**CanaKit Raspberry Pi 4 4GB Starter PRO Kit**
The pre-installed-HAOS kit went out of stock (checked Aug 2026) — this is
the swap-in replacement. Same board tier (4GB, worth it over the 2GB
minimum given 2026's memory-driven price hikes closed the gap anyway),
reputable brand, includes case/PSU/microSD. One difference: the microSD
comes preloaded with plain Raspberry Pi OS, not HAOS, so you'll flash
Home Assistant OS onto it yourself — 10 minutes with the free Raspberry Pi
Imager tool (imager.raspberrypi.com), pick "Home Assistant" under
"Other specific-purpose OS" in the tool's OS list. README.md step 1 updated
with this.
~$80-110
https://amazon.com/CanaKit-Raspberry-4GB-Starter-Kit/dp/B07V5JTMV9

## Sensor nodes (3x — lung room, 4x4, 5x5)

**ELEGOO 3-Pack ESP32 Dev Boards (ESP-WROOM-32, USB-C)**
Exactly the 3 you need, one purchase.
~$20-25 for the 3-pack
https://www.amazon.com/ELEGOO-ESP-WROOM-32-Development-Bluetooth-Microcontroller/dp/B0D8T53CQ5

**AITRIP 3-Pack Pre-Soldered BME280 Sensors**
Temp/humidity/pressure, I2C, no soldering. Exactly 3 in one purchase.
~$18-25 for the 3-pack
https://www.amazon.com/AITRIP-Pre-Soldered-Temperature-Atmospheric-Barometric/dp/B0CG95GVVD

**Female-to-female (F-F) Dupont jumper wires, 20cm** — both the ESP32 and
BME280 have male pin headers, so F-F bridges them with no breadboard
needed. 4 wires per node (3V3/GND/SCL/SDA) x 3 nodes = 12 needed; buy a
40-pack for spares and color-coding room. Any 40-pack of F-F 20cm jumpers works.
~$6-8
https://www.amazon.com/California-JOS-Breadboard-Optional-Multicolored/dp/B0BRTJQGS6

**Plus:** 3 small project boxes, or just use the tent's own frame with zip
ties (~$5-10 total). No specific link — generic, grab whatever's cheapest.

## Smart plugs (4x needed — humidifier, dehumidifier, 4x4 light, 5x5 light)

**Kasa Matter Smart Plug, KP125M (4-pack)** — cheaper pick, recommended
Revised call from the original Shelly recommendation: Matter is a
local-only protocol by design — paired straight into Home Assistant's
built-in Matter integration, no TP-Link cloud account touched at any
point, not even during setup. Same "never depends on a vendor's cloud"
guarantee the Shelly pick was chasing, for less money, plus a spare plug
out of the 4-pack. Setup needs HA's Matter integration enabled (built in,
free — see README).
~$40-70 for 4 (seen as low as $40 April 2026)
https://www.amazon.com/Kasa-Smart-Monitoring-Compact-Certified/dp/B0BYGMR4DX

**Shelly Plug US Gen4** — original pick, still solid if you'd rather skip
touching HA's Matter integration setup
Shelly's own local API also has no cloud-account requirement ever. Costs
more per plug but nothing extra to enable in HA.
~$18-25 each, x4 = ~$72-100
https://us.shelly.com/products/shelly-plug-us-gen4-black

## Cameras (optional)

The build uses 2x Reolink E1 (base model) — one for the 4x4, one for the
5x5. Nothing to add to the
total; wired into the build in README.md step 5 and live on the dashboard.

## Total (using the Kasa Matter recommendation)

| Item | Cost | When |
|---|---|---|
| ESP32 3-pack | $20-25 | Buy now |
| BME280 3-pack | $18-25 | Buy now |
| Jumper wires | $6-8 | Buy now |
| Misc enclosures | $5-10 | Buy now |
| Kasa Matter KP125M 4-pack | $40-70 | Buy now |
| **Buy-now total** | **~$89-138** | |
| Pi 4 hub (CanaKit, flash HAOS yourself) | $80-110 | Deferred — laptop's covering this for now |
| **Full total (once you get the Pi)** | **~$169-248** | |

If you go Shelly instead of Kasa Matter, add ~$15-30 back on top of the
above (4x Shelly at $72-100 vs. the Kasa 4-pack's $40-70).

## Adding another tent later (optional)

The 3-packs above give you exactly 3 nodes with nothing spare, so each extra
tent with its own climate sensor is one more ESP32 (~$7-9) + one more BME280
(~$8-10), about $15-20.
