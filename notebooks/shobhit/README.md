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

## References

1. Final presentation slides and verification results: [ECE 445 Final Presentation-1.pdf](../../ECE%20445%20Final%20Presentation-1.pdf).
2. ECE 445 Lab Notebook guide in [`guide/`](../../guide).
3. Project CAD and build photos in [`imgs/`](../../imgs).
