# Task: add the golden v2 power stage to the Sthymuli motherboard schematic

You are working in the KiCad project at `C:\Users\Nathann\OneDrive\ClawdDrive\Electronique\Sthymuli-motherboard`. Copy this folder next to it first. The source of truth is `power-stage-netlist.json` in this folder: every component, its LCSC number and every pin-to-net connection. The drawings are at https://claude.ai/artifact/JhpvjoJL54coHkT74R9dAr; use them only as a picture, because the JSON is authoritative.

## What to build
Four new hierarchical sheets, or one "power" sheet with four sections, whichever fits the existing project:

| Sheet (JSON `sheet`) | Content |
|---|---|
| `charger` | USB VBUS → PTC → blocking diode → CN3801 P-FET buck → 30 mΩ sense → P+ |
| `pack` | Main cell + PTC, HY2112-AB with M1/M2 back-to-back on the negative side, VBAT and NTC sensing |
| `boost_5v` | TPS61088 → +5V (5.10 V, 3 A) |
| `buckboost_3v3` | TPS63802 → +3V3 (3.31 V, always on above 2.6 V) |

Components with `sheet` = `cardinal_N/E/W` (F4–F6) belong on the three cardinal-board schematics, next to each cell holder.

## Rules
1. **Symbols and footprints.** Use the project library `lib/sthymuli_jlc` (made with easyeda2kicad). If a part is missing, generate it first:
   ```powershell
   $ids = @("C2892380","C22452","C77091","C5224296","C167223","C154643","C45783","C2889049","C168763","C5185318","C87357","C167228","C2845237","C840499","C20917","C5290175")
   foreach ($id in $ids) { easyeda2kicad --full --lcsc_id=$id --output ".\lib\sthymuli_jlc" --overwrite }
   ```
   For parts marked `basic`, use KiCad's generic `Device:R` / `Device:C` symbols with the footprint hint given, for example `Resistor_SMD:R_0402_1005Metric`. Add the LCSC number as a field named `LCSC` on every symbol; JLC-basic passives get their JLC basic part number.
2. **Pin mapping.** Pin keys in the JSON are `"<number> <name>"`. For example, `"1-3 S"` means pins 1, 2 and 3 all go to that net, and `K`/`A` are diode cathode and anode. Before wiring, check that the generated symbol's pin numbers match the JSON. If a pin number differs, stop and report it rather than guessing; this matters most for Q1 (AO4435) and M1/M2 (WSD20100DN56), whose pinouts were not verified.
3. **Nets.**
   - Use global labels for `P+`, `B-`, `GND`, `+5V`, `+3V3` and `VBUS_IN`, and local labels for everything else.
   - **`B-` and `GND` are different nets and must never be joined**, including through a power symbol or a GND flag. B− is the cell negative before the protection FETs; GND is after M2.
   - Use `PWR_FLAG` where ERC needs it.
4. **ESP32 signals.** `esp32_signals` in the JSON lists signals that go to the ESP32-S3 sheet. Connect them with labels and pick free pins: ADC1 pins (GPIO1–10) for `NTC_ADC` and `VBAT_ADC`.
5. **Remove the old power section.** Remove the old 4S BMS / BQ25713 / BQ76905 power section only after asking me; list what you would delete first.
6. **Keep these markers.** Values marked `VERIFY` in the JSON notes (R10, R13, C13) and parts marked `TBD` (F3, L3) stay as placeholders, with a text note on the sheet saying so.
7. **Check your work.** Run ERC with `kicad-cli sch erc --severity-all <project>.kicad_sch` and fix what belongs to the new sheets. Then export a netlist with `kicad-cli sch export netlist` and compare every net in `power-stage-netlist.json` against it. Report any difference.

## Single-pin nets (expected)
These connect off-sheet and appear only once in the JSON:
- `VBUS_IN`: comes from the USB-C / CH224D sheet.
- `CELL_N+`, `CELL_E+`, `CELL_W+`: go to the cell holders on the cardinal boards.
- `ESP_*`: go to ESP32 GPIOs.

## Layout notes (for later, not this task)
- **Boost:** keep the input loop tight (C10–L2–U3 PGND), because it carries about 7 A peak. Put C12 next to the VOUT pins and use the pad as the thermal path.
- **Charger:** route the CSP and BAT sense lines as a Kelvin pair from the RCS pads.
- **Protection:** give M1/M2 and the P+/B−/GND paths wide copper, sized for about 7 A continuous.
