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

## 2026-03-05

**Objective:** Decide what debug access the firmware team needed on the PCB.

**Work completed:** I listed the signals worth exposing during bring-up: power rails, ground, UART, reset, boot, at least one drive PWM channel, and the weapon control PWM line. I also outlined a staged debug process so motor hardware would not be energized before basic controller health was confirmed.

**Design decisions:** Test points are worth the board area because they reduce ambiguity during integration. For this project, the cost of not being able to isolate a wiring or firmware issue was higher than the cost of a few extra copper features.

**Alternatives considered:** A denser board without labeled test points would have looked cleaner, but would have slowed down bring-up and fault isolation.

**Equations/calculations:** The staged verification sequence implicitly follows dependency order: `power good -> MCU boots -> firmware runs -> PWM visible -> actuator responds`.

**Testing/debugging results:** No hardware results yet, but this became the checklist used for later bring-up sessions.

**Partner summary:** Rahul was deciding how many signals were practical to expose on the board and how to route them. Shobhit was setting up the Fusion workflow so the PCB and connectors could be referenced mechanically.

**Next steps:** Finish the PCB and transition from planning to integration-ready hardware.

## 2026-04-01

**Objective:** Refine the control and power assumptions using the partially assembled electrical and mechanical design.

**Work completed:** I summarized the system progress and updated the firmware-side power assumptions, especially the distinction between idle logic load and highly variable motor load. I noted that battery-life calculations would be dominated by motor use, but the controller still needed a stable logic rail during aggressive motion or weapon startup.

**Design decisions:** I treated logic stability as the gating requirement rather than optimizing overall battery runtime, because a brownout in the controller would be more damaging to system behavior than inefficient current draw.

**Alternatives considered:** Running closer to the edge on regulator margin might have saved space or component count, but it would have increased reset risk under load transients.

**Equations/calculations:** Battery runtime was tracked generically as `t_runtime = Capacity / I_avg`. Even without final current data, that equation kept the team focused on separating continuous electronics load from intermittent actuator load.

**Figures/diagrams/photos:** Figure A1 shows the drive motors being test-fit into the printed chassis, which directly constrained wire routing and future integration work.

![Figure A1 - Drive motors fitted into the chassis on 2026-04-01](../../imgs/fitting%20drive%20motors%20into%20chassis%20%202026-04-01%20at%203.03.01%20PM.jpeg)

**Testing/debugging results:** At this point the meaningful result was packaging progress rather than control validation. The mechanical fit check reduced uncertainty about board and harness placement.

**Partner summary:** Rahul refined the power tree and current-budget reasoning. Shobhit was validating that the printed geometry could accept the motors and leave enough room for internal hardware.

**Next steps:** Complete a first full control path from MCU output to motor behavior.

## 2026-04-08

**Objective:** Investigate the brushless weapon motor bring-up problem and determine whether the custom driver path was viable.

**Work completed:** I logged a debugging session around the brushless weapon motor behavior. The issue was that the motor did not cleanly transition into normal spin; instead, the observed behavior suggested startup trouble or misconfiguration. I treated the problem as a combined firmware/driver/state issue and started organizing the information needed to separate register configuration mistakes from wiring or motor-parameter problems. The initial custom path was based on the MCF8316A sensorless BLDC driver, so the debug process had to account for both control signaling and device configuration state [6].

**Design decisions:** I kept the debugging record centered on observable behavior, control settings, and likely fault classes instead of jumping to a single explanation. That structure mattered because later discussions about switching to an external ESC needed traceable justification.

**Alternatives considered:** We could have continued iterating on the custom brushless driver indefinitely, but that path risked consuming too much of the schedule. The alternative was to preserve the custom board for the rest of the system and externalize weapon commutation to an ESC.

**Equations/calculations:** No closed-form solution was available, but I tracked the dependency that startup success depends on correct commutation parameters, current limits, and an internally consistent command interface.

**Figures/diagrams/photos:** Figure A2 shows the brushless motor selected for the weapon path. Figure A3 shows the CAD assembly state on the same date, which is relevant because a weapon integration decision affected both control and packaging.

![Figure A2 - Brushless weapon motor under evaluation](../../imgs/brushless_motor_image.jpeg)

![Figure A3 - CAD assembly with skids, motor holder, and weapon on 2026-04-08](../../imgs/chassis%20assembly%20cad%20with%20skids%20motor%20holder%20and%20weapon%202026-04-08%20at%208.06.23%20PM.jpeg)

**Testing/debugging results:** The non-routine result was failed or inconsistent startup of the weapon motor. This became the technical basis for considering an external ESC.

**Partner summary:** Rahul analyzed the electrical/fault side of the brushless driver path. Shobhit reviewed the mounting implications of startup vibration and the safety envelope around the spinning weapon.

**Next steps:** Either stabilize the custom brushless control path quickly or switch to an external ESC to protect the schedule.

## 2026-04-21

**Objective:** Build a practical drive-control interface using the ESP32 and an Xbox-controller-driven host script.

**Work completed:** I developed the control-side architecture for drive testing. The ESP32 accepted command updates, and a Python layer converted Xbox controller input into motor commands. I explicitly considered deadzone behavior, motor inversion, range limiting, and update rate so the robot would be controllable even if the final tuning was still rough. This control split fit the ESP32-C3-WROOM-02 feature set and kept wireless command handling and PWM generation on the embedded side [3].

**Design decisions:** I kept controller interpretation off-board in Python while keeping actuation on the ESP32. That split made rapid iteration easier because input mapping could change without reflashing the robot for every small adjustment.

**Code snippet:** I used this control-mixing logic to convert the Xbox joystick values into left/right motor PWM commands before sending them to the ESP32.

```python
MIN_PWM = 160
MAX_PWM = 255
DEADZONE = 0.15
EXPO = 2.0

def apply_deadzone(value):
    if abs(value) < DEADZONE:
        return 0.0
    sign = 1 if value > 0 else -1
    mag = (abs(value) - DEADZONE) / (1.0 - DEADZONE)
    return sign * (mag ** EXPO)

def scale_to_pwm(value):
    if value == 0:
        return 0
    sign = 1 if value > 0 else -1
    mag = min(abs(value), 1.0)
    return sign * round(MIN_PWM + mag * (MAX_PWM - MIN_PWM))

def compute_motor_commands(forward_raw, turn_raw):
    forward = apply_deadzone(forward_raw)
    turn = apply_deadzone(turn_raw)
    left = forward + turn
    right = forward - turn
    scale = max(abs(left), abs(right), 1.0)
    return scale_to_pwm(left / scale), scale_to_pwm(right / scale)
```

**Alternatives considered:** Directly hardcoding a fixed autonomous or canned-motion test would have been faster, but it would not have exercised the real remote-control workflow needed for the final system.

**Equations/calculations:** A useful control constraint was recorded for later overvoltage reasoning: if a 12 V nominal motor is driven from a 16.8 V pack, a first-order average-voltage match is `D = 12 / 16.8 = 0.714`, or about `71.4%` duty cycle. I also noted that this average-voltage argument does not eliminate transient or stall-current risk.

**Testing/debugging results:** The important result was that the command path was now concrete enough to validate motor response, latency, and sign conventions. Remaining issues were tuning and integration rather than a missing control framework.

**Partner summary:** Rahul supported electrical bring-up by checking control lines, grounds, and driver behavior. Shobhit was adjusting printed tolerances and hardware fit so the assembly could survive repeated bench tests.

**Next steps:** Integrate weapon control into the same operator workflow and validate the full robot under floor testing.

## 2026-04-28

**Objective:** Move weapon control to an external ESC and debug the new PWM control path.

**Work completed:** After the custom brushless path continued to consume schedule, I treated the external ESC as the fastest route to a reliable weapon subsystem. I updated the control assumptions to servo-style PWM timing and logged the mismatch possibilities that could explain incorrect ESC behavior, including pulse-width range, arming/calibration sequence, and common-ground problems. This switch happened after the earlier MCF8316A-based approach proved difficult to configure repeatably [1][6].

**Design decisions:** Migrating the weapon channel to a commercial ESC reduced firmware scope and concentrated our effort on producing a valid command signal. This was a pragmatic integration decision rather than a perfect architectural one.

**Alternatives considered:** Staying on the custom brushless driver would have kept a more self-contained board-level design, but it was no longer the lowest-risk path to a working demonstration.

**Equations/calculations:** I modeled the new control path as pulse-position signaling rather than duty-cycle-only signaling. The key requirement became correct pulse width and update timing, not just an average PWM percentage.

**Code snippet:** For the ESC version, I mapped the operator command range `0..255` into a servo-style pulse width and kept a single `stopAllMotors()` path for both drive and weapon shutdown.

```cpp
#define ESC_PULSE_MIN 1000
#define ESC_PULSE_MAX 2000
#define WM_INPUT_MAX  255

int currentWM = 0;

int clampWeapon(int value) {
  if (value < 0) return 0;
  if (value > WM_INPUT_MAX) return WM_INPUT_MAX;
  return value;
}

void setWeaponThrottle(int throttle) {
  currentWM = clampWeapon(throttle);
  long pulseUs = ESC_PULSE_MIN +
    ((long)currentWM * (ESC_PULSE_MAX - ESC_PULSE_MIN)) / WM_INPUT_MAX;
  weaponEsc.speed((int)pulseUs);
}

void stopAllMotors() {
  setMotor1(0);
  setMotor2(0);
  setWeaponThrottle(0);
}
```

**Testing/debugging results:** The notable non-routine result was ESC beeping and unstable behavior at higher commands, which pointed toward calibration or signal interpretation issues rather than a solved control path.

**Partner summary:** Rahul documented ESC wiring, the common-ground requirement, and the LiPo short incident risk analysis. Shobhit updated packaging assumptions because the ESC introduced additional wiring volume and thermal/mounting constraints.

**Next steps:** Finish ESC signal validation, then move to integrated floor tests with the assembled chassis.

## 2026-04-30

**Objective:** Consolidate the project into a final system explanation and capture the integrated state of the robot.

**Work completed:** I prepared the system-level story for the presentation: problem definition, architecture, key tradeoffs, debugging history, and final operational state. I also recorded how the control design matured through multiple integration choices rather than only listing the final working parts.

**Design decisions:** I emphasized traceability from design risk to integration change. In particular, the decision to move weapon control to an external ESC was documented as an engineering trade rather than an arbitrary late swap.

**Alternatives considered:** A purely feature-focused presentation would have hidden the debugging logic. I instead chose to make the integration narrative explicit because it better reflects real engineering development.

**Figures/diagrams/photos:** Figure A4 shows the final CAD assembly used to communicate the integrated layout before the fully assembled robot photo set was complete.

![Figure A4 - Final CAD assembly on 2026-04-30](../../imgs/final%20cad%20assembly%202026-04-30%20at%203.26.15%20PM.jpeg)

**Testing/debugging results:** The system was close enough to final form that documentation and verification could now be tied to concrete integrated hardware, not just subsystem sketches.

**Partner summary:** Rahul finalized the electrical design explanation, especially protection and power-path choices. Shobhit finalized the wheel protector and last mechanical refinements needed for a cleaner final assembly.

**Next steps:** Perform last procurement/risk checks and capture the finished robot in its final build state.

## 2026-05-01

**Objective:** Check the impact of last-minute motor and printed-part constraints on controllability and schedule.

**Work completed:** I revisited the control implications of using replacement or borderline-rated motors with a 4S pack. The main question was whether duty-cycle limiting could safely constrain average voltage for drive motors if ideal replacements were unavailable. I also reviewed the printed-part mass updates because they affect acceleration and control feel.

**Design decisions:** Any temporary use of 12 V hardware on a 16.8 V max pack had to be treated as a constrained-risk compromise, not as proof of full compatibility.

**Alternatives considered:** Waiting for perfectly matched replacement motors would have been cleaner electrically, but schedule pressure made it necessary to at least quantify the PWM-limiting option.

**Equations/calculations:** The previously derived `71.4%` duty-cycle estimate for a 12 V average-equivalent command on a 4S pack remained the control reference. I noted again that this only addresses average voltage, not peak electrical stress.

**Figures/diagrams/photos:** Figure A5 shows the printed parts being weighed on 2026-05-01, which fed into the final integration picture.

![Figure A5 - Weighing newly printed parts on 2026-05-01](../../imgs/weighing%20newly%20printed%20parts%202026-05-01%20at%203.04.07%20PM.jpeg)

**Testing/debugging results:** This was mostly a risk-management session. The main output was a clearer boundary between acceptable temporary operation and unacceptable overvoltage assumptions.

**Partner summary:** Rahul checked compatibility from the electrical side, including current draw and thermal risk. Shobhit checked whether any replacement hardware would still fit the existing printed geometry.

**Next steps:** Capture final photos and final verification results.

## 2026-05-03

**Objective:** Record the final integrated robot state and summarize verification status.

**Work completed:** I documented the final assembled system and the integrated floor-test state of the robot. By this point the notebook showed the full design arc: concept, control architecture, PCB, mechanical packaging, driver debugging, ESC migration, and final assembled platform. During demo-day testing, end-to-end wireless latency measured 75 ms, command updates were stable at 50 Hz over a 15-foot line-of-sight link, and firmware safety logic disabled motor outputs within 250 ms of communication loss [1].

**Design decisions:** The final narrative emphasized what actually shipped as the working system: custom control electronics for the drive path, ESP32-based command generation, a printed chassis, and an externally controlled weapon subsystem where necessary to preserve reliability.

**Code snippet:** The final firmware safety path used a command watchdog so the motors would shut down if the controller stopped sending updates.

```cpp
static const uint32_t COMMAND_TIMEOUT_MS = 250;
volatile uint32_t lastCommandMillis = 0;
bool watchdogStopped = false;

void handleCommand(String cmd) {
  lastCommandMillis = millis();
  watchdogStopped = false;
  // Parse m1=...,m2=...,wm=... and update outputs here.
}

void loop() {
  if (!watchdogStopped && millis() - lastCommandMillis > COMMAND_TIMEOUT_MS) {
    stopAllMotors();
    watchdogStopped = true;
    sendBleMessage("{\"watchdog\":\"timeout\"}");
  }
  delay(20);
}
```

**Figures/diagrams/photos:** Figure A6 shows the floor-test configuration from 2026-04-27, and Figure A7 shows the final assembled robot from 2026-05-03.

![Figure A6 - Integrated testing session on 2026-04-27](../../imgs/testing%20session%202026-04-27%20at%209.52.51%20PM.png)

![Figure A7 - Final battlebot on 2026-05-03](../../imgs/the%20final%20battlebot%202026-05-03%20at%206.14.40%20PM.jpeg)

**Testing/debugging results:** The final control checks met the project requirements: the wireless link remained stable at 15 feet, response stayed under the 100 ms requirement, and comm-loss shutdown stayed within the 250 ms requirement [1].

**Partner summary:** Rahul closed out the electrical explanation and protection discussion. Shobhit closed out the final mechanical assembly, fit, and protective geometry.

**Next steps:** Capture raw controller logs, PWM traces, and repeatable weapon spin-up data during future full-system tests.

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
