# Rahul Lab Notebook

## Project Summary

**Project:** Combative Hardened Ultra Tumbler (`C.H.U.T.`), a compact battlebot using a custom control board, LiPo power, separate drive and weapon motor paths, and a printed chassis. The electrical requirements were a stable 3.3 V rail within 3.0-3.6 V, no brownout under load, wireless response under 100 ms, and safe shutdown within 250 ms of communication loss [1].

**Primary responsibilities:** power architecture, motor-driver selection, PCB schematic/layout work, protection decisions, ESC migration for the weapon motor, and electrical debugging.

## 2026-02-13

**Objective:** Define the electrical architecture required by the battlebot concept.

**Work completed:** I broke the proposed robot into power domains and actuator classes. Two drive motors required a bidirectional brushed-motor solution, while the weapon path implied a higher-risk brushless subsystem. I also identified that the board needed battery entry, regulation for logic rails, connectors for motors, and a programming/debug path for the ESP32.

**Design decisions:** The electrical design would separate logic supply concerns from high-current actuator concerns. That separation was essential for a robot that might see large transient loads and occasional impacts.

**Alternatives considered:** A single undifferentiated motor-control approach for both drive and weapon channels would have simplified the block diagram, but would have hidden major differences in control method and current behavior.

**Equations/calculations:** The first relevant battery bounds were `V_3S,max = 3 * 4.2 V = 12.6 V` and `V_4S,max = 4 * 4.2 V = 16.8 V`. Those values framed every later compatibility discussion.

**Testing/debugging results:** No hardware testing yet. This was an architecture-definition session.

**Partner summary:** Abhinav concentrated on the control interface and the firmware assumptions behind PWM-based actuation. Shobhit started mapping component envelopes into a printable mechanical volume.

**Next steps:** Choose candidate driver ICs and decide whether 3S or 4S operation was more realistic for the performance target.

## 2026-02-24

**Objective:** Review the schematic-level support circuitry around the ESP32 and power entry.

**Work completed:** I reviewed the likely USB-UART support path, boot/reset behavior, and the need for a robust battery-to-logic power chain. I also considered where protection belonged, including transient suppression, switch placement, and clean ground reference between logic and power hardware. The board architecture was converging on an ESP32-C3-WROOM-02 controller, DRV8871 brushed-motor drivers, and an LMR51430 buck converter, so the review focused on making those pieces coexist cleanly [3][4][5].

**Design decisions:** Reliable programming, reset, and power-up behavior were treated as board-level requirements, not conveniences. I also decided that the design needed clear connectorization and accessible test points because the final robot would not be easy to probe after assembly.

**Alternatives considered:** Leaning too hard on external debug hardware would have simplified the board but would have made repeated assembly-stage debugging much harder.

**Equations/calculations:** No new numeric derivation, but I kept the power-path dependency explicit: `battery input -> protection/switching -> buck conversion -> logic rail`.

**Testing/debugging results:** This was a design review session, but it directly shaped later bring-up and reduced the chance of un-debuggable power faults.

**Partner summary:** Abhinav defined which MCU pins and control channels needed to be observable during bring-up. Shobhit incorporated connector and access needs into the early packaging plan.

**Next steps:** Translate the reviewed architecture into a routed PCB that preserves debug accessibility.

## References

1. Final presentation slides and verification results: [ECE 445 Final Presentation-1.pdf](../../ECE%20445%20Final%20Presentation-1.pdf).
2. ECE 445 Lab Notebook guide in [`guide/`](../../guide).
3. Espressif, [ESP32-C3-WROOM-02 & ESP32-C3-WROOM-02U Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-c3-wroom-02_datasheet_en.pdf).
4. Texas Instruments, [DRV8871 product page and datasheet](https://www.ti.com/product/DRV8871).
5. Texas Instruments, [LMR51430 product page and datasheet](https://www.ti.com/product/LMR51430).
6. Texas Instruments, [MCF8316A product page and datasheet](https://www.ti.com/product/MCF8316A).
7. Final schematic screenshot: [`imgs/full_schematic screenshot.png`](../../imgs/full_schematic%20screenshot.png).
8. Final PCB routing screenshot: [`imgs/route_pcb_image.png`](../../imgs/route_pcb_image.png).
9. Project images in [`imgs/`](../../imgs).
