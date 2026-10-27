# Proposal: custom firmware for the physical rack

**Draft, 2026-10-27.** This document assesses feasibility, then proposes instruments within the documented limits. The designs below are original project suggestions, not claims about features already implemented or measured.

## 1. Where the premise can fail

The largest obstacle is access to the hardware. A microprocessor may only handle MIDI, presets, or panel scanning. Its code may be protected, its peripherals undocumented, or its audio path mostly fixed analog circuitry. Even an accessible processor has specific converter, memory, timing, and interface limits.

AI assistance can reduce source-reading, implementation, and build-system work. A successful compilation still leaves the electrical behavior, real-time performance, and musical usefulness to establish on the actual module.

The owner has identified Via modules and a development plate, µO_C, Mutable clones, Z-DSP, and NE, Qu-Bit, and Behringer equipment as candidates. Exact model and revision details remain incomplete; see the [inventory](HARDWARE_INVENTORY.md). Faceplate count does not establish the number of Via processing boards.

## 2. Feasibility and priority

"Documented" means that a developer route was found in primary sources. It does not mean a compatible build has been reproduced here.

| Target | Evidence available | Main unresolved point | Proposed priority |
|---|---|---|---|
| Starling Via | Shared firmware core, hardware build projects, resource tools, Rack implementation | Installed hardware/expander; calibration handling for custom images | First hardware project |
| µO_C | Open firmware and established applet frameworks | Teensy generation, board variant, existing firmware | Next small control project |
| Z-DSP | Manufacturer-documented FV-1 cartridge development | Programmer and rewritable cartridges available? | Early DSP project if programming equipment is available |
| Digital Mutable clones | Original firmware and hardware sources | Which clones, their revisions, and available update interfaces | Select one after inventory |
| NE Versio | Manufacturer explicitly documents custom libDaisy firmware | Whether an owned NE module is a Versio; exact revision | First general audio-DSP candidate if owned |
| NE Legio | Manufacturer explicitly documents custom libDaisy firmware | Whether owned; exact control and pitch-input mapping | Separate compact voice project |
| Qu-Bit Aurora | Manufacturer's public SDK and examples | Whether owned; hardware revision and source licenses | Audio-DSP candidate if owned |
| Qu-Bit Nebulae v2 | Public repository and alternate-instrument documentation | Whether owned; Pi version and installed instrument environment | Sample/Pd candidate if owned |
| Other NE / Qu-Bit | Model-specific investigation needed | Factory firmware exchange does not itself establish a custom-code route | Research queue |
| Behringer | No exact model or custom-code route established in this proposal | MCU role, board map, boot/update interface, recovery | Research queue |

Priority reflects the presently identified equipment and documentation. It should change if inventory reveals a more suitable module or missing programming equipment.

## 3. Starling Via: first project

### Established route

Starling publishes a cross-platform core targeting STM32F373 hardware and Rack, with separate platform drivers. It also provides resource-generation tools. The physical circuit includes track-and-hold stages and VCAs; firmware has to work with that particular topology. The documentation describes calibration preservation and notes that Viaflash's local-firmware path does not automatically save or restore presets.

Sources: [shared core](https://github.com/starlingcode/Via), [hardware projects](https://github.com/starlingcode/via_hardware_executables), [Rack implementation](https://github.com/starlingcode/Via-for-Rack), [resource tools](https://github.com/starlingcode/viatools), [platform and Viaflash documentation](https://starling.space/via/platform-info), [hardware/development documentation](https://starling.space/via/diy).

### Experiment A: one custom resource bank

Choose one installed personality and change a single bank of patterns, ratios, scales, or waveforms through the existing tools. For example, make a GATESEQ pattern bank with sparse, asymmetric accents intended for the owner's patches, or a SYNC-family ratio collection. Confirm the resource format for the selected personality before choosing the data.

This establishes the resource pipeline and audible result with a smaller change than a complete firmware rewrite. Test it in the matching Rack module where resource loading is supported. Preserve the stock bank for comparison.

### Experiment B: clocked contour instrument

Develop one mode with a defined relationship between clock, reset, phase, and contour shape. Its proposed distinction is a precisely specified reset/clock contract and a control layout chosen for the development plate. Audit the existing SYNC, META, and ATSR implementations first; extend an existing personality if it already supplies the desired behavior.

| Control | Proposed meaning, subject to verified board mapping |
|---|---|
| Three firmware parameter knobs | Clock ratio, contour shape, phase offset |
| Main logic input | Clock |
| Auxiliary logic input, if expander is fitted | Reset |
| CV inputs | Modulate the three parameters within documented ranges |
| Existing analog A/B controls and inputs | Retain their physical signal-path functions |
| Touch controls | Mode/state operations with a printed control legend; provide reset here when the auxiliary input is unavailable |

Use the available generated-signal and logic outputs according to the actual board map. Keep track-and-hold/VCA behavior explicit; do not assume independent stereo processing or arbitrary audio routing.

**Initial behavior contract:** reset takes priority over a clock event sampled in the same processing tick; reset arms the next clock to start at phase zero; clock loss finishes the current contour and holds its endpoint; a resumed clock starts a new contour. Label this policy on the control sheet and revise it if playing tests show a different policy is preferable.

**Completion evidence:** matching Rack/hardware source revisions; a working resource or firmware image; a one-page panel map; reset and clock-loss traces; measured latency/jitter over the declared operating range; verified calibration retention and a successful stock restore. Rack auditioning does not establish the analog hardware's exact behavior.

## 4. µO_C: a small control instrument

### Established route

O_C firmware and the Hemisphere/Phazerville ecosystem provide an existing development base. Current Phazerville documentation distinguishes original O_C hardware, Teensy variants, and O.R.N.8. Features and build targets depend on that distinction.

Sources: [original firmware documentation](https://ornament-and-cri.me/firmware/), [Phazerville source](https://github.com/djphazer/O_C-Phazerville), [installation](https://firmware.phazerville.com/Installation), [build choices](https://firmware.phazerville.com/Build-Choices), [existing applet index](https://firmware.phazerville.com/).

### Proposed applet: seeded gate and accent

Begin with one Hemisphere-sized applet: two digital inputs for clock/reset, CV inputs for probability and variation, and two outputs for gate and accent CV. A fixed seed and explicit reset reproduce a 16-step phrase for the same sampled input sequence. The screen shows the position, seed, and active settings.

Compare existing rhythm, random, and reset applets before adding a new one. Prefer a small contribution to a suitable existing applet if the missing behavior is just reset semantics or a control mapping.

Gorgon can drive ordinary clock/reset/CV through suitable existing interfaces. No USB runtime link, audio capability, or eight-bit digital bus is assumed for the owner's µO_C.

**Completion evidence:** build for the identified Teensy/board; repeatable event traces for a fixed seed and sampled controls; tests of simultaneous clock/reset, tempo changes, and declared gate widths; measured output scaling and timing on hardware. External analog variation can change the sampled inputs even when the seed is unchanged.

## 5. Digital Mutable clones: select one targeted modification

### Established route

Mutable's repository publishes firmware and hardware designs, with licenses differing between AVR and STM32 projects. Model-specific developer pages document toolchains and programming methods. The clone's hardware and bootloader still need checking; analog-only modules are outside this firmware proposal.

Sources: [original repository and licenses](https://github.com/pichenettes/eurorack), [Clouds development example](https://pichenettes.github.io/mutable-instruments-documentation/modules/clouds/open_source/), [Marbles development/calibration example](https://pichenette.readthedocs.io/en/latest/modules/marbles/open_source/).

### Candidate modifications, conditional on ownership

| Compatible clone | Focused experiment | Main feasibility check |
|---|---|---|
| Clouds | A held grain window with an explicit recapture rule and performance-oriented control scaling | Available trigger mapping, buffer behavior, CPU/memory headroom |
| Stages | A repeatable CV trajectory with a documented reset/retrigger policy | Existing stock/alternate firmware coverage, segment and input mapping |
| Warps | One audio bit-reduction/folding algorithm with musically chosen CV response | Converter path, processing budget, and available parameter controls |

These are design candidates. Inventory and review of existing alternate firmware should select the smallest useful change rather than committing to all three.

Reproduce the stock build first using its documented toolchain. Preserve model-specific calibration and settings. An update-by-audio mechanism, serial loader, and debugger are distinct routes; record only the one verified for the selected clone.

**Completion evidence:** a matching baseline and modified build; an A/B audio or CV example; CPU/code-size accounting; control-range tests; and stock restore on the identified unit.

## 6. Z-DSP: original cartridge programs

### Established route

Tiptop documents SpinAsm development for the Z-DSP's FV-1 and transfer to blank cartridges using NumberZ, plus display-text tools. The proposed deliverable is a cartridge program bank. Instruction, arithmetic, and delay-memory limits come from the FV-1 architecture.

Sources: [Tiptop development tools](https://tiptopaudio.com/pc-sw-drivers/), [Z-DSP platform](https://tiptopaudio.com/zdsp-ns/), [FV-1 programming guide](https://www.spinsemi.com/knowledge_base/pgm_quick.html), [instruction reference](https://www.spinsemi.com/knowledge_base/inst_syntax.html).

### Proposed bank

Start with two programs: a short diffusion delay and a held, slowly modulated diffuse tail. Prototype the smallest delay network that fits the assembler's reported budgets. Give each program three clear parameter assignments and matching display text.

An eventual eight-program bank could explore variations in diffusion, modulation, feedback filtering, and deliberate fixed-point coloration. Defer the remaining slots until the first two are useful in the physical feedback patch.

Spoon Bender can supply musical reference material. Its desktop engine and arithmetic are not presumed compatible with FV-1 assembly; implement selected ideas specifically for this chip. Changes to the Z-DSP clock also affect physical delay durations and modulation rates, which need documenting.

**Completion evidence:** assembler output and resource usage; cartridge/display programming record; impulse, silence, feedback, and control-extreme renders; physical listening and clock-range checks. Confirm access to a programmer and rewritable cartridges before scheduling the hardware milestone.

## 7. Noise Engineering: separate hardware families

### Established route

Noise Engineering explicitly directs Versio and Legio developers to libDaisy and example projects. This establishes a custom-firmware route for those families. It does not establish that the manufacturer's DSP algorithms are published, that their binaries are interchangeable between families, or that other NE products have the same route.

Sources: [manufacturer FAQ](https://noiseengineering.us/pages/faq/), [Versio development guide](https://noiseengineering.us/blogs/loquelic-literitas-the-blog/create-your-own-firmware-on-a-versio-module/), [libDaisy](https://github.com/daisyaudio/libDaisy), [Daisy examples](https://github.com/daisyaudio/DaisyExamples), [board API documentation](https://electro-smith.github.io/libDaisy/group__boards.html).

### Versio candidate: clocked stereo hold/delay

Use a bounded stereo buffer with clock divisions, feedback filtering, and a hold request that takes effect at the next clock boundary. Define how the delay behaves when the clock stops and how it moves between time values. Verify the exact trigger, knob, and switch map before fixing the panel legend.

Start with delay/hold and smooth controls. Add degradation or diffusion only after CPU and memory measurements. The result should be one playable effect with an explicit performance behavior.

### Legio candidate: compact phase-distortion voice

A single voice with a small set of phase-distortion shapes, pitch input, timbre CV, and controlled transitions is a plausible separate project. Evaluate individual ideas from the CZ work in DSP_Emulation; establish algorithm correctness, license compatibility, and processing cost before reusing code. This proposal makes no claim to a complete hardware CZ emulation.

**Completion evidence for either:** reproduce a board-specific upstream example; pin the toolchain, SDK, and dependency revisions; record sample rate, callback size, memory use, and worst-case processing margin; check control extremes and pitch or delay behavior; restore official firmware. Audit any owned Alia or older NE modules separately.

## 8. Qu-Bit: Aurora and Nebulae have different routes

### Aurora candidate: held diffusion instrument

Qu-Bit's public Aurora SDK includes hardware access, audio/control examples, build instructions, and a USB-drive installation path. A public SDK is evidence of a development route; the chosen files and dependencies still need their licenses recorded before code reuse or distribution.

Sources: [Aurora SDK](https://github.com/Qu-Bit-Electronix/Aurora-SDK), [documentation sources](https://github.com/Qu-Bit-Electronix/Aurora-SDK/tree/main/docs), [official firmware catalog](https://www.qubitelectronix.com/alternate-firmware).

Begin with the SDK's audio pass-through example, then a small diffusion network with explicit capture/hold and controlled decay. Add repeatable modulation only after profiling. Verify the actual input mapping before promising a clocked or triggered capture interface. Aurora and Versio may eventually share a portable algorithm, but their hardware adapters and firmware images remain separate.

**Completion evidence:** example build; documented control map; impulse and hold/release tests; processing/memory measurements; physical audition; official restore.

### Nebulae v2 candidate: sample-window instrument

Qu-Bit provides a public Nebulae v2 repository, alternate instruments, a developer guide, and a Pure Data template. Official disk images distinguish Pi 3 and Pi 4 hardware.

Sources: [Nebulae repository](https://github.com/Qu-Bit-Electronix/QB_Nebulae_V2), [developer guide and alternate instruments](https://www.qubitelectronix.com/alternate-instruments), [disk images and firmware catalog](https://www.qubitelectronix.com/alt-firmware-summary).

Use the alternate-instrument interface first: one sample-window/slice player with bounded jumps, envelopes at splice boundaries, and an explicit hold/recapture operation. Confirm which installed runtime and controls the selected instrument format exposes. A Pure Data version offers a familiar development path without replacing the full operating-system image.

**Completion evidence:** instrument loads alongside stock; representative sample files and control-extreme tests; measured pitch, trigger response, and CPU use; a documented return to the stock instrument. A full disk reimage is a separate operation with calibration implications.

Other Qu-Bit models remain model-specific research tasks. Their presence in a factory download catalog alone is insufficient evidence of an SDK or custom-code loader.

## 9. Behringer: one model-specific feasibility study

Select one owned model and revision. Determine the processor's actual role, available source/documentation, external control options, update format, and any documented programming/recovery interface. Identify whether the useful opportunity is sequencing, MIDI behavior, panel operation, or audio DSP.

The first deliverable is an evidence-backed board/development-route note. Replacement firmware is a later decision based on that note. No universal Behringer target or generic flashing procedure is proposed here, and this document makes no claim that their products are uniformly open or uniformly inaccessible.

## 10. Implementation sequence

| Milestone | Work | Evidence needed to finish |
|---|---|---|
| M0 — identify targets | Complete the relevant inventory row; select upstream revisions and record licenses | Exact board/MCU, controls/I/O, stock restore route, known calibration storage |
| M1 — Via baseline | Build the selected upstream Rack and hardware targets; document resource/calibration handling | Build logs and an unmodified baseline that can be restored |
| M2 — Via experiment | Custom resource bank, then one contour mode if still useful | Panel map, meaningful behavior tests, Rack audition, physical measurements |
| M3 — second target | O_C applet, or Z-DSP programs if cartridge tools are ready | One useful, independently installable experiment |
| M4 — audio platform | One confirmed NE or Qu-Bit target, or one Mutable clone | Board-specific baseline, profiled custom behavior, hardware results |
| M5 — wider reuse | Extract code only when two working targets demonstrate a useful common part | Equivalent core behavior with target-specific numerical/timing tolerances |

These are dependency milestones, not calendar estimates. Research can continue while physical testing is pending; a hardware milestone remains open until its measurements exist.

## 11. Repository and testing approach

Keep the repository small during the proposal stage. Add a target directory when a baseline is selected. It should contain a short instrument brief, pinned upstream revisions, build instructions, source/adaptation changes, a control map, and target-specific test results. Add resource generators where useful. Introduce a shared core only after demonstrated reuse.

Build instructions should work on the development Linux machine where supported. Older projects may need their original compiler or a pinned environment; upgrading every toolchain is a separate task. Keep building an image separate from writing it to hardware.

For control algorithms, test reset ordering, clock loss/restart, seeded repeatability for identical sampled inputs, and output boundaries. For DSP, render impulses, representative audio, silence, and control extremes; check discontinuities, sustained feedback, memory bounds, and processing headroom. Physical measurements establish actual input/output scaling, latency, jitter, and pitch behavior. An emulator or host test cannot establish those electrical properties.

When firmware artifacts are eventually published, identify board revision, source commit, dependency/toolchain versions, checksums, control map, installation/restore instructions, and the extent of testing. Label build-only, simulated, and hardware-tested results distinctly. Calibration/preset storage needs an explicit preservation or restore method for each target.

## 12. Relationship to Gorgon, ByteBus, and existing DSP work

[DSP_Emulation](https://github.com/JosiahAlanLee/DSP_Emulation) is a source of reference behavior and musical ideas to inspect, not evidence that a desktop engine fits an embedded target. Record the license and numerical assumptions of any code selected for reuse.

Start physical integration with ordinary clock, reset, CV, and audio. The software ByteBus expander connection in Rack is not a physical bus connector on Via, O_C, or the other modules. A future encoded-voltage, serial, USB, or MIDI transport would need compatible physical I/O, an explicit protocol, and its own timing/voltage tests.

Gorgon integration can therefore start as a patch using existing interfaces. Firmware should retain a useful standalone interface and a printed control map. A workstation can generate sample material or resource banks for a module without requiring that module to run the workstation's model or software stack.
