# DAY 2: Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles

## Hierarchical vs Flat Synthesis

### Hierarchical Design

A **Hierarchical Design** consists of multiple sub-modules instantiated in the 'top' module. The hierarchical design approach is taken for large designs to gain the advantage of a "divide and conquer" approach. This leads to better utilization of tool resources for proper optimization of smaller designs, as a larger design is difficult for a tool to optimize efficiently. It helps in better utilization of compute resources during the design process and also leads to faster runtime and debugging operations.

-----

### Flat Design

A **flat design** approach is chosen when the design is sufficiently small for the tool to optimize efficiently in a reasonable amount of time. For small and simple ASICs, a flat approach is preferable, while a hierarchical approach is better for larger, complex ASICs. A flat design is a representation where all hierarchical module structures are collapsed into a single level, eliminating submodules and integrating their logic directly into the top-level module.

-----

## Design of Multiple Modules
![Alt Text]()
### Hierarchical Flow

This approach is used to preserve the design hierarchy.

  - **Top Module:** `multiple_modules`
  - **Sub Modules:** `sub_module1`, `sub_module2`

**Commands:**

```bash
yosys
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog multiple_modules.v
synth -top multiple_modules
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr multiple_modules.v
```
![Alt Text]()
### Flattened Flow

**Commands:**

```bash
# Inside yosys after synthesis
flatten
write_verilog -noattr multiple_modules_flat.v
```
![Alt Text]()
-----

## Efficient Flop Coding Styles

### D Flip-Flop with Async Reset
![Alt Text]()
This flip-flop captures the input `d` on the rising edge of the clock, unless the asynchronous reset is activated. In the `dff_asyncres` module, the asynchronous reset has higher priority than the clock. `async_reset` is checked first, so if it is high (1), it immediately resets `q` to 0.
![Alt Text]()
**Synthesis Commands:**

```bash
yosys
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_reset.v
synth -top dff_async_reset
dfflibmap -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text]()
### D Flip-Flop with Async Set
![Alt Text]()
This flip-flop captures input `d` on the rising edge of the clock, unless the asynchronous set is active. The flip-flop responds to either the positive edge of `clk` or `async_set`. If `async_set` is high, the output `q` is immediately set to 1, regardless of the clock.
![Alt Text]()
**Synthesis Commands:**

```bash
yosys
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_async_set.v
synth -top dff_async_set
dfflibmap -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text]()
### D Flip-Flop with Sync Reset

![Alt Text]()
This flip-flop captures the value of `d` on the rising edge of the clock, unless the synchronous reset is active. The `always` block triggers only on the rising edge of the clock. If `sync_reset` is high at the time of the clock edge, the output `q` is set to 0.
![Alt Text]()
**Synthesis Commands:**

```bash
yosys
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_sync_reset.v
synth -top dff_sync_reset
dfflibmap -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
![Alt Text]()
**Note on Commands:**

  * `abc -liberty <.lib file path>`: This command is used for technology mapping of Yosys’s internal gate library to a target architecture.
  * `synth -top <module_name>`: This command runs the Yosys synthesis script on the specified top module of the design.
  * `dfflibmap -liberty <.lib file path>`: This command maps internal flip-flop cells to the flip-flop cells in the technology library specified in the given liberty file.

-----

## Optimizations

Optimization in synthesis refers to simplifying the RTL logic to make the design more efficient in terms of area, power, and timing.

### Synthesis of MULT2
![Alt Text]()
**Commands:**

```bash
yosys
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_2.v
synth -top mult_2
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr mult_2_net.v
```
![Alt Text]()
### Part 2 - Synthesis of mult8
![Alt Text]()
**Commands:**

```bash
yosys
read_liberty -lib ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog mult_8.v
synth -top mult8
abc -liberty ~/sky130RTLDesignAndSynthesisWorkshop/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr mult_8_net.v
```
![Alt Text]()
