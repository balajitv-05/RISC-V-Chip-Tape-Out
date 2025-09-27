# DAY 4 - GLS
----- 
## Synthesis-Simulation Mismatches

## What is a Synthesis-Simulation Mismatch?

A **Synthesis–Simulation Mismatch (SSM)** happens when the behavior of a digital circuit in pre-synthesis simulation (RTL simulation) does not match the behavior of the same circuit after it has been synthesized into a gate-level netlist. This is a critical issue because it means the verified RTL code does not represent the final hardware. 

-----

## Causes of Synthesis–Simulation Mismatch

### Incomplete Sensitivity List (in Verilog/VHDL)

  * **RTL Simulation:** If a signal is missing from a combinational `always` block's sensitivity list, the simulator will only trigger the block when a listed signal changes, potentially missing updates.
  * **Synthesis:** The synthesis tool, however, infers purely combinational logic from the block's content and creates a circuit that is sensitive to *all* inputs, regardless of the list.

### Blocking vs. Non-Blocking Assignments

  * Incorrect use of `=` (blocking) instead of `<=` (non-blocking) in sequential logic can cause major discrepancies.
  * **RTL Simulation:** The simulator evaluates statements in order. A blocking assignment can create a race condition that might resolve in an expected way during simulation.
  * **Synthesis:** The synthesized hardware evaluates all inputs to flip-flops simultaneously at the clock edge, leading to different behavior than the sequentially-evaluated simulation.

### Unintended Latch Inference

  * This occurs when an `if` or `case` statement in a combinational block does not account for all possible conditions, lacking a final `else` or a `default` case.
  * **RTL Simulation:** Simulators might show a stable, predictable output (often retaining the previous value).
  * **Synthesis:** The synthesis tool must preserve this "memory" behavior, so it infers a latch, creating unintended state-holding elements in the design.

### Initialization Differences

  * **RTL Simulation:** Simulators often initialize registers to a default value, like `X` (unknown) or `0`.
  * **Synthesis:** Real hardware (flip-flops) powers up to an unknown, random state. Unless explicit reset logic is designed and coded, the hardware will not initialize to a known value.

### Timing-Related Issues

  * **RTL Simulation:** This is a zero-delay or delta-delay simulation. It assumes logic gates and wires are instantaneous.
  * **Gate-level Simulation:** This simulation uses the actual propagation delays from the technology library. This can reveal timing issues like glitches and hazards that are invisible in the RTL simulation.

### Unsupported Constructs

  * Some Verilog/VHDL constructs are created purely for simulation and testbenches. They have no physical hardware equivalent.
  * Examples include `initial`, `#delay`, `fork-join` blocks, and file I/O operations (`$fopen`, `$display`). Synthesis tools will ignore these, leading to a mismatch.

-----

## How to Avoid SSM 

  * Always write **synthesizable RTL code**.
  * Use a complete sensitivity list. In Verilog, `always @(*)` is recommended for combinational logic.
  * Strictly use **non-blocking assignments (`<=`)** for sequential logic (in `always_ff` or `always @(posedge clk)` blocks).
  * Provide **proper, explicit reset logic** for all registers to ensure a known starting state.
  * Avoid non-synthesizable constructs in your design files. Keep them in the testbench only.
  * Run **linting tools** and synthesis checks to catch potential issues early.

-----

## Commands
### For iverilog based simulation
```bash
iverilog file.v tb_file.v
./a.out
gtkwave tb_file.vcd
```
### For iverilog based GLS
#### Structure of GLS
![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/gls.png)

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v file_net.v tb_file.v
./a.out
gtkwave tb_file.vcd
```
### For Yosys
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
read_verilog file.v
synth -top module_name
dfflibmap -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
write_verilog -noattr file_net.v
```
## LAB Examples

### Example 1 - Ternary Operator Mux

#### Verilog Code

```verilog
module ternary_operator_mux (input i1 , input i1 , input sel , output y);
assign y = sel?i1:i0;
endmodule
```

#### Waveform before Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/ternery_operate_mux_sim.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/ternery_operator_syn.png)
#### Synthesized Diagram
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/ternery_operate_show.png)

#### After GLS
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/ternery_after_gls.png)
### Example 2 - Bad Mux

#### Verilog Code

```verilog
module bad_mux (input i0 , input i1 , input sel , output reg y);
always @ (sel)
begin
	if(sel)
		y <= i1;
	else 
		y <= i0;
end
endmodule
```

#### Waveform before Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/bad_mux_sim.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/bad_mux_syn.png)
#### After GLS Waveform
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/bad_mux_gls.png)

### Example 3 - Blocking Caveat

#### Verilog Code

```verilog
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

#### Waveform before Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/bloxking_sim.png)
#### For Synthesis
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/blocking_syn.png)
#### Synthesized Diagram
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/blocking_show.png)

#### After GLS Waveform
![Alt Text](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/384e092b2631756a562a9ea72c76212874a69bb6/week1/Day4/Day4_images/blocking_gls.png)
