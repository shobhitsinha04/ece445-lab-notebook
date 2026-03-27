# Shobhit Lab Notebook

## Project Summary

**Project:** Combative Hardened Ultra Tumbler (`C.H.U.T.`), a compact battlebot with a printed chassis, two driven wheels, a front weapon assembly, and integrated electronics. The mechanical design was constrained by antweight rules: nominal `2 lb` class, wireless operation, and fabrication from printable thermoplastics such as PLA/PLA+ [1].

**Primary responsibilities:** chassis layout, motor mounts, skid plates, wheel selection, printed-part tolerances, weapon mounting, and final outer-shell refinement.

## 2026-02-13

**Objective:** Establish the first mechanical constraints for the battlebot concept.

**Work completed:** I translated the abstract battlebot idea into a packaging problem. The chassis had to contain battery, PCB, drive motors, wiring, and a front-mounted weapon while staying printable and serviceable. I also identified the value of a low, wide, rectangular body because it would be easier to print and easier to organize internally.

**Design decisions:** I chose to think about the design as a protected box with defined internal zones rather than a sculpted shell. That made integration with the electrical team more practical.

**Alternatives considered:** A more aggressive or curved shell could have looked better, but would have made mounting and print iteration slower.

**Equations/calculations:** The first geometric rule I noted was clearance-based rather than numeric: every major subsystem needed both footprint area and wire-routing volume, not just nominal component dimensions.

**Testing/debugging results:** No fabrication yet. This was a constraint-definition session.

**Partner summary:** Abhinav defined how the control system would connect to the actuators. Rahul defined the likely power and PCB blocks that had to be reserved inside the shell.

**Next steps:** Start CAD with a top-down layout driven by the board, motors, battery, and weapon envelope.

## 2026-03-27

**Objective:** Create the first motor-arm geometry for the front weapon support.

**Work completed:** I sketched and modeled the first version of the arm or side-support geometry used to hold the weapon motor and front assembly. The early design focused on keeping the front structure printable and attaching it cleanly to the main box body.

**Design decisions:** I used planar, bolt-together geometry instead of a more organic shape so revisions would be faster and easier to dimension.

**Code snippet:** I kept the first motor-arm geometry parameterized so the length, pivot diameter, and mounting-tab width could be changed without redrawing the entire sketch.

```python
arm_length_mm = 110
arm_width_mm = 27
pivot_diameter_mm = 27
mount_tab_mm = 10
plate_thickness_mm = 8

def motor_arm_envelope():
    return {
        "body": (arm_length_mm, arm_width_mm, plate_thickness_mm),
        "pivot_cutout_diameter": pivot_diameter_mm,
        "mount_tab_width": mount_tab_mm,
    }
```

**Alternatives considered:** Printing the front assembly as a fully blended one-piece structure would have reduced fasteners, but it would have made failure recovery and iteration harder.

**Figures/diagrams/photos:** Figure S1 shows the first weapon-arm sketch, and Figure S2 shows the first modeled motor-arm version on 2026-03-27.

![Figure S1 - Initial motor-arm sketch](../../imgs/initial_motor_arm_sketch.jpeg)

![Figure S2 - First motor-arm version on 2026-03-27](../../imgs/motor_arm_first_version%202026-03-27%20at%2012.39.02%20PM.jpeg)

**Testing/debugging results:** The result was a manufacturable first pass, not a finished part. The design still needed fit checks and likely reinforcement.

**Partner summary:** Abhinav used the emerging geometry to understand how the weapon would affect control-space and wiring. Rahul communicated connector and PCB constraints that had to remain accessible.

**Next steps:** Print the main chassis and begin real fit checks.

## References

1. Final presentation slides and verification results: [ECE 445 Final Presentation-1.pdf](../../ECE%20445%20Final%20Presentation-1.pdf).
2. ECE 445 Lab Notebook guide in [`guide/`](../../guide).
3. Project CAD and build photos in [`imgs/`](../../imgs).
