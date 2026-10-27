# Hardware inventory

Recorded from the owner's statements on **2026-10-27**. Unknown entries are intentionally unresolved; platform documentation is not a substitute for identifying the installed board.

## Currently known

| Platform | Reported equipment | Still needed before a hardware build |
|---|---|---|
| Starling Via | Via modules, a development plate, and approximately five other physical faceplates | Processing-board count/revisions, faceplate/personality names, expander presence, installed firmware, calibration/restore method |
| µO_C | At least one µO_C | Manufacturer, board revision, Teensy 3.2/4.0/4.1, voltage-range variant, installed firmware |
| Mutable clones | Clones owned; individual models unspecified | Original module represented, clone manufacturer/revision, MCU, installed firmware/bootloader, accessible update interface |
| Tiptop Z-DSP | Z-DSP owned | Hardware revision, access to NumberZ or another verified compatible cartridge-programming route, rewritable cartridges |
| Noise Engineering | Modules owned; individual models unspecified | Names, hardware revisions, and whether any belong to Versio or Legio; investigate other families individually |
| Qu-Bit | Modules owned; individual models unspecified | Names/revisions; establish whether Aurora or Nebulae v2 is present; Pi version for Nebulae v2 |
| Behringer | Equipment owned; individual models unspecified | Exact product/revision, MCU role, documentation, source availability, update/recovery interface |

A spare faceplate is an interface option, not an additional processor. The Via development plate's exact layout should be checked before assigning controls or producing an overlay.

## Record for each selected unit

Fill this out when selecting a target; no need to disassemble every module before starting Via research.

| Field | Value |
|---|---|
| Module / manufacturer | TBD |
| Board revision / MCU | TBD |
| Current firmware / bootloader | TBD |
| Panel and expander configuration | TBD |
| Verified inputs, outputs, ranges, and control map | TBD |
| Upstream repository / pinned commit | TBD |
| Source and dependency licenses | TBD |
| Build environment / toolchain | TBD |
| Installation route and compatible image format | TBD |
| Stock image/source and restore method | TBD |
| Calibration/preset locations and preservation method | TBD |
| Programming equipment already available | TBD |
| Bench-test method and results | TBD |

Avoid committing per-unit firmware dumps, calibration blobs, or vendor binaries by default. Document where the owner keeps their unit's backup; publish only artifacts intended for distribution with appropriate provenance.

## First inventory task

Identify one Via board, its expander configuration, and its current personality. Confirm access to a stock restore and a calibration-preserving custom-image workflow. Use that unit to establish the first reproducible build and resource experiment described in the [proposal](PLATFORM_PROPOSAL.md#3-starling-via-first-project).
