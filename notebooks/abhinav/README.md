# Abhinav Lab Notebook

## Project Summary

**Project:** Combative Hardened Ultra Tumbler (`C.H.U.T.`), a compact battlebot with two drive motors, an ESP32-based controller, a brushless weapon motor, printed chassis components, and a custom PCB for power and control. The final project requirements emphasized wireless control, response under 100 ms, shutdown within 250 ms of communication loss, drivetrain speed around 2 m/s, and weapon speed above 2000 RPM [1].

**Primary responsibilities:** firmware architecture, controller interface, PWM/motor control, integration testing, ESC migration, and final system verification.

## 2026-02-13

**Objective:** Define the initial system concept and convert the battlebot idea into a buildable ECE 445 project proposal.

**Work completed:** I helped formalize the battlebot as a constrained embedded systems project instead of a vague robotics idea. The first pass at the system partition was battery, power regulation, ESP32 controller, two drive motors, one weapon motor, custom PCB, and printed chassis. I also identified that the control subsystem had to do more than simple on/off actuation; the robot needed independent left/right drive authority and a safe method to arm or disarm the weapon.

**Design decisions:** We treated the project as an integration problem with three major interfaces: electrical power, mechanical packaging, and real-time control. That framing made it easier to assign work and to define testable requirements.

**Alternatives considered:** A simpler remote-control vehicle without a weapon would have reduced risk, but it would not have exercised enough custom embedded design. A fully custom brushless control path for every motor was also discussed indirectly, but that increased firmware and driver complexity too early.

**Equations/calculations:** At this stage I recorded the control relationship that would drive later firmware design: average motor voltage under PWM is approximated by `V_avg = D * V_batt`, where `D` is duty cycle.

**Testing/debugging results:** No hardware testing yet. This entry established the control requirements that future tests would verify.

**Partner summary:** Rahul focused on motor/power architecture and likely driver choices. Shobhit started assessing whether the mass, wheel placement, and weapon geometry could fit into a printable chassis.

**Next steps:** Finalize the power tree, choose the control microcontroller, and define a motor-control interface that can be exercised before full mechanical integration.

## 2026-02-17

**Objective:** Translate high-level system requirements into a control architecture for the drive and weapon subsystems.

**Work completed:** I wrote down the control surfaces the firmware had to expose: left drive command, right drive command, weapon enable, and weapon speed command. I also noted the need for deadman behavior so loss of controller input would default the robot to a safe state. This session established that PWM would be the central actuator interface for both drive control and later ESC experiments.

**Design decisions:** I separated drive control from weapon control conceptually, because the drive motors needed bidirectional behavior while the weapon path had much stricter startup and safety concerns.

**Alternatives considered:** A single mixed drive command could have been computed off-board and sent as one steering/throttle pair, but exposing per-side control made debugging easier and reduced ambiguity during bring-up.

**Equations/calculations:** For a 4-cell LiPo, the fully charged pack voltage is `4 * 4.2 V = 16.8 V`. This value became the upper bound for any PWM-based command calculations and for later motor overvoltage risk discussions.

**Testing/debugging results:** No bench test yet. The result of the session was a clearer control contract for hardware and firmware interfaces.

**Partner summary:** Rahul continued reviewing current and voltage constraints for the motor paths. Shobhit used motor and battery size assumptions to reserve physical space in the chassis model.

**Next steps:** Align control pins with the emerging schematic and identify which signals need to be exposed for debug.

## 2026-02-24

**Objective:** Plan the ESP32 programming and reset path so firmware bring-up would not block later integration.

**Work completed:** I reviewed the ESP32 programming flow and the supporting USB-to-UART/reset circuitry needed for reliable flashing. I identified the importance of access to reset, boot, UART, and PWM pins during bring-up. This was also when I started treating debug accessibility as part of the firmware design instead of an afterthought. The ESP32-C3-WROOM-02 module documentation was the main reference for boot behavior, pin use, and the available wireless/peripheral features [3].

**Design decisions:** The board needed explicit support for programming and reset rather than assuming one-time firmware loading. A repeatable flash/debug loop was more important than minimizing parts count.

**Alternatives considered:** Using an external USB-UART adapter without onboard support would have simplified the PCB slightly, but it would have made debugging in the assembled robot more awkward.

**Equations/calculations:** No new numeric calculation. I documented a signal dependency instead: `flashability = f(power rail stability, reset path, boot strap correctness, UART access)`.

**Testing/debugging results:** This was a planning session. The main deliverable was a signal list for later board review and firmware bring-up.

**Partner summary:** Rahul was reviewing the CP2102, EN/BOOT support, protection, and general schematic integrity. Shobhit was translating connector and access needs into mechanical cutouts and cable clearance.

**Next steps:** Define a bring-up order: verify rails, verify boot, verify serial output, then verify PWM outputs before connecting power hardware.

## References

1. Final presentation slides and verification results: [ECE 445 Final Presentation-1.pdf](../../ECE%20445%20Final%20Presentation-1.pdf).
2. ECE 445 Lab Notebook guide in [`guide/`](../../guide).
3. Espressif, [ESP32-C3-WROOM-02 & ESP32-C3-WROOM-02U Datasheet](https://www.espressif.com/sites/default/files/documentation/esp32-c3-wroom-02_datasheet_en.pdf).
4. Texas Instruments, [DRV8871 product page and datasheet](https://www.ti.com/product/DRV8871).
5. Texas Instruments, [LMR51430 product page and datasheet](https://www.ti.com/product/LMR51430).
6. Texas Instruments, [MCF8316A product page and datasheet](https://www.ti.com/product/MCF8316A).
7. Final schematic screenshot: [`imgs/full_schematic screenshot.png`](../../imgs/full_schematic%20screenshot.png).
8. Final PCB routing screenshot: [`imgs/route_pcb_image.png`](../../imgs/route_pcb_image.png).
9. Project photos and CAD screenshots in [`imgs/`](../../imgs).
