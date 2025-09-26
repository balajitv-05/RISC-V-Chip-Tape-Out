# DAY 3: Logic Optimization

## What is Logic Optimization?

Logic optimization in VLSI is the process of improving a digital circuit’s design to **minimize area, delay, and power consumption** while preserving its functionality. It is a crucial step in the synthesis phase of VLSI design, performed before the physical implementation. 🧠

-----

## Objectives of Logic Optimization

  * **Minimize Area:** Reduce the number of gates and interconnections.
  * **Minimize Delay:** Shorten the critical path to improve circuit speed. ⏱️
  * **Minimize Power:** Reduce switching activity and overall capacitance. 🔋
  * **Improve Reliability:** Simplify the design to reduce glitches and hazards.
  * **Reduce Fabrication Cost:** Smaller circuits cost less and are easier to manufacture.

-----

## Types of Optimization

### Boolean-Level Optimization

This involves simplifying Boolean expressions to reduce the total number of gates.

**Techniques:**

  * **Algebraic simplification:** Using Boolean algebra rules (e.g., $A + AB = A$).
  * **Karnaugh Maps (K-map):** A manual, visual method for simplification, ideal for small circuits.
  * **Quine–McCluskey method:** A systematic tabular method that's more practical for larger circuits than K-maps.
  * **Espresso algorithm:** A popular heuristic algorithm for efficiently optimizing large combinational circuits.

### Gate-Level (Structural) Optimization

This focuses on optimizing the arrangement and type of gates for better area, delay, and power.

**Techniques:**

  * **Gate Restructuring:** Rewriting logic using alternative gate combinations.
  * **Technology Mapping:** Converting generic logic gates to the specific gates available in the target technology library (e.g., sky130).
  * **Common Subexpression Elimination:** Identifying and sharing repeated logic structures to save area.

### Technology-Dependent Optimization

This stage optimizes the circuit for a specific technology library (e.g., CMOS, TTL, FPGA, ASIC).

**Techniques:**

  * **Gate sizing:** Adjusting the transistor sizes within a gate to find the best trade-off between delay and power.
  * **Buffer insertion:** Adding buffers to long wires to reduce signal propagation delay.
  * **Fan-out optimization:** Replicating gates to drive large loads, minimizing the impact on critical paths.

### Delay (Performance) Optimization

The primary goal is to minimize the critical path delay to improve the circuit's maximum operating speed.

**Techniques:**

  * Logic restructuring to balance path delays.
  * Pipeline insertion for high-speed, high-throughput circuits.
  * Parallel execution of independent logic blocks.

### Power Optimization

This aims to reduce both dynamic (switching) and static (leakage) power consumption.

**Techniques:**

  * Reducing switching activity by reordering operations or using gray codes.
  * **Clock gating:** Disabling the clock to portions of the circuit that are not in use.
  * Using low-power logic styles (e.g., pass-transistor logic).

### Redundancy Removal

This involves removing any logic that does not affect the final outputs of the circuit.

**Techniques:**

  * Detect and eliminate redundant gates or terms.
  * Use advanced methods like SAT-based or BDD-based analysis to identify hidden redundancies.

### High-Level Optimization (Behavioral/RTL)

This optimization occurs during RTL synthesis, before the design is mapped to gates.

**Techniques:**

  * **Strength reduction:** Replacing computationally expensive operations with cheaper ones (e.g., replacing multiplication by a constant '2' with a simple left shift).
  * **Resource sharing:** Using a single hardware unit (like an adder) for multiple operations that occur at different times.
  * Loop unrolling and pipelining in sequential circuits.

-----

## Optimization Commands in Yosys

The primary command for cleaning up and removing redundant logic after synthesis is:

```bash
opt_clean -purge
```

### Combinational Logic Optimization
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/opt_check/Screenshot%20from%202025-09-26%2015-09-36.png)
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt_clean -purge
write_verilog -noattr opt_check.v
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/opt_check/Screenshot%20from%202025-09-26%2015-12-29.png)
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/opt_check/Screenshot%20from%202025-09-26%2015-14-37.png)
### Multiple Module Optimization
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/multi_module_opt/Screenshot%20from%202025-09-26%2016-02-39.png)
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/multi_module_opt/Screenshot%20from%202025-09-26%2016-05-45.png)


### Sequential Logic Optimization
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/dff_const/dff_c12_des.png)
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/dff_const/dff_const1_sim.png)
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
opt_clean -purge
write_verilog -noattr dff_const1_net.v
```
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/dff_const/dff_const1_syn.png)
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/dff_const/dff_const1_show.png)
### Sequential Optimization for Unused Cells
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/counter_opt.png)
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/counter_opt_sim.png)![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/counter_opt_syn.png)
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/813756f5b8c32456d99753547a01434bb9eb858b/week1/Day3/Day3_images/counter_opt_show.png)
