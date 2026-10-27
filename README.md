# Firmware

Custom firmware proposals for the physical modular rack, with VCV Rack and desktop tools used for development where the target supports them.

**Status: proposal only.** No custom firmware has been built, flashed, or tested by this repository yet.

## Start here

- **[Platform proposal](docs/PLATFORM_PROPOSAL.md)** — development routes, instrument ideas, constraints, and an implementation order.
- **[Hardware inventory](docs/HARDWARE_INVENTORY.md)** — confirmed equipment and the exact models/revisions still needed before hardware builds.

## Proposed targets

| Platform | First proposed experiment | Development route |
|---|---|---|
| **Starling Via** | Custom resource bank, then a clocked contour instrument for the development plate | Published shared core, hardware projects, and Rack implementation |
| **µO_C / Ornament & Crime** | Resettable, seeded gate/accent applet | Existing O_C / Hemisphere / Phazerville code; exact Teensy target to confirm |
| **Digital Mutable clones** | One focused capture, modulation, or control change on a confirmed clone | Original source and model-specific hardware/update documentation |
| **Tiptop Z-DSP** | Two original short-delay/diffusion programs, eventually an eight-program card | FV-1 assembly and programmable cartridges |
| **NE Versio / Legio** | Clocked stereo hold/delay on Versio; compact phase-distortion voice on Legio | Manufacturer-documented libDaisy development |
| **Qu-Bit Aurora / Nebulae v2** | Held diffusion effect on Aurora; sample-window instrument on Nebulae | Public Aurora SDK; Nebulae alternate-instrument interface |
| **Other NE, Qu-Bit, and Behringer modules** | Identify one exact model and establish its programming route | Investigation; custom firmware access is not yet established here |

These routes are documented at the platform level. Compatibility with the owner's particular units still needs confirmation. References and qualifications are in the proposal; a downloadable factory update alone does not establish support for arbitrary replacement firmware.

## First milestone

Start with **Via**, for which the owner reports a development plate and approximately five other faceplates. Confirm the electronics and expander, record the installed firmware and calibration, reproduce an upstream build, and audition a small resource change in Rack before attempting a new instrument.

The next deliverable would be one buildable, documented experiment with a matching control map and an explicit stock-firmware restore procedure.

## Related work

- [DSP_Emulation](https://github.com/JosiahAlanLee/DSP_Emulation): reference implementations and musical ideas to evaluate for embedded targets.
- [Gorgon](https://github.com/JosiahAlanLee/Gorgon): possible external clock/reset/CV source; any richer firmware protocol requires a separate specification.

Each hardware family keeps its own build and installation path. Shared algorithm code is a possibility to establish through working ports, rather than an assumption about every processor in the rack.

Proposal evidence reviewed: **2026-10-27**.
