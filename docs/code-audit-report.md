# Feather template correctness audit

Date: 2026-10-26. Scope: legacy schematic, cached symbols, PCB, project settings, template metadata, README installation, and existing licence. KiCad CLI nightly 10.99.0 checks ran on temporary copies; original design data was retained.

## Corrected documentation and metadata

[meta/info.html](../meta/info.html) now identifies the Micro-USB template, fixes the original description typo, includes image alternative text, and explains that routing, power, and application circuitry remain to be designed. Original arturo182 attribution and the upstream licence are retained. The README documents the legacy symbol cache and 3D-model path dependency.

## Findings requiring a completed design

- **P1 — This is an incomplete electrical design.** [Adafruit_Feather.kicad_pcb](../Adafruit_Feather.kicad_pcb#L6) has zero tracks. The schematic supplies connector labels and USB/battery power nets, but no controller, regulator, charging circuit, or application. Micro-USB D+, D−, and ID remain unconnected. ERC on the legacy source reports **29 findings: five errors** (two undriven power inputs and three unconnected USB pins) **and 24 isolated-label warnings**. These are not suppressed; they must be resolved according to the derived circuit's requirements.
- **P2 — Modern manufacturing checks are not clean.** DRC using default nightly constraints and current standard footprint libraries reports **45 violations** (32 copper-edge clearance errors, five solder-mask bridge errors, eight library-footprint mismatch warnings), plus **10 unrouted connections**. For example, header-pad copper has 0.42 mm edge clearance against the tested 0.5 mm constraint. Select actual fabrication constraints and review connector mask openings when completing the design; changing limits merely to clear the report would not establish manufacturability.
- **P2 — Mounting geometry needs explicit compatibility review.** The [PCB header placement](../Adafruit_Feather.kicad_pcb#L361) has 2.54 mm pin pitch and 20.32 mm row separation, and its main outline is 50.8 × 22.86 mm, consistent with the nominal [Feather specification](https://learn.adafruit.com/adafruit-feather/feather-specification). However, the [mounting footprints](../Adafruit_Feather.kicad_pcb#L293) use two 2.5 mm USB-side holes and two 2.2 mm far-side holes. The far-side centres are shifted outward by 0.635 mm relative to the USB-side rows. The specification describes 2.54 mm corner holes; this template therefore does not establish enclosure/mounting compatibility. No hole or outline was silently relocated.
- **P2 — Legacy assets need deliberate migration.** Current standard footprints differ from the embedded 2020 copies. Four model paths use `${KISYS3DMOD}` and historical `.wrl` names; the tested nightly installation does not include those exact files. Keep the supplied symbol cache and verify library/model mappings when moving to a newer KiCad release. A full conversion and 3D-alignment review were not performed.

## Checks and evidence

The schematic exported a one-page SVG and an XML netlist with four connectors. A separate comparison grouped every connector pin by net in the schematic and PCB: **all 31 pin/net groups matched**, including USB VBUS, battery polarity, power-header positions, and signal-header labels. Every assigned footprint name exists in the installed standard footprint libraries. Mechanical dimensions were read from source coordinates; connector dimensions were not recertified against manufacturer drawings.

Commands ran against a temporary copy under `/tmp/makeristic-library-template-audit/`, with `XDG_CONFIG_HOME` pointing to a temporary `config` directory. Standard symbol/footprint tables were configured only in that temporary environment:

```sh
kicad-cli-nightly sch export netlist --format kicadxml --output netlist.xml Adafruit_Feather.sch
kicad-cli-nightly sch export svg --output schematic-svg Adafruit_Feather.sch
kicad-cli-nightly sch erc --format json --output erc.json Adafruit_Feather.sch
kicad-cli-nightly pcb drc --format json --output drc.json Adafruit_Feather.kicad_pcb
```

The output directory was created before SVG export. Direct CLI schematic upgrade and built-in schematic-parity DRC rejected the legacy `.sch` format (`Expecting '('`); they are not claimed as passing. The netlist comparison above used the successful legacy export instead. No converted schematic or generated audit artifacts were committed.

Relative documentation links, template image paths, and `git diff --check` passed. ERC/DRC reports contain the findings above; command completion is not a clean design sign-off. Physical assembly, electrical behaviour, and manufacturing were not tested.
