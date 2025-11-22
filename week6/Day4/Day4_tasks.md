# 🔧 Day 4 — Custom Cell Integration & STA

Comprehensive guide to custom inverter cell creation, LEF/DEF generation, and Static Timing Analysis (STA).

## 📋 Table of Contents

1.  [Overview](#overview)
2.  [Track Configuration & Grid Setup](#-section-1-track-configuration--grid-setup)
3.  [Port Definition & LEF Generation](#-section-2-port-definition--lef-generation)
4.  [Custom Cell Integration into OpenLane](#-section-3-custom-cell-integration-into-openlane)
5.  [Timing Analysis & Optimization](#-section-4-timing-analysis--optimization)
6.  [Floorplan to Post-CTS Timing](#️-section-5-floorplan-to-post-cts-timing)

-----

## 🎯 Overview {#overview}

This session focuses on **pre-layout timing analysis** and demonstrates the critical importance of robust clock tree design in achieving timing closure. The workflow involves integrating a custom standard cell (inverter), performing detailed Static Timing Analysis (STA) using OpenSTA, optimizing timing through synthesis parameter tuning, and implementing Clock Tree Synthesis (CTS).

**Key Objectives:**

  - ✅ Configure track-based grids to ensure standard cell alignment.
  - ✅ Define I/O ports and generate Library Exchange Format (LEF) files.
  - ✅ Integrate custom cells into the OpenLane automated flow.
  - ✅ Perform pre-layout STA and apply timing optimization strategies.
  - ✅ Execute Clock Tree Synthesis (CTS) to build a balanced clock network.
  - ✅ Analyze post-CTS timing, focusing on Setup and Hold slack.

-----

## 📐 Section 1: Track Configuration & Grid Setup {#-section-1-track-configuration--grid-setup}

### 1.1 Understanding Track Specifications

In physical design, tracks represent the available routing resources on specific metal layers. Standard cells must be aligned with these tracks to ensure the router can successfully connect input and output pins.

**Track Definition:**

  - **Horizontal (X) Tracks:** Spaced at **0.46µm** intervals.
  - **Vertical (Y) Tracks:** Spaced at **0.34µm** intervals.
  - **Layer Consistency:** Each metal layer possesses defined routing tracks in both X and Y directions.

### 1.2 Grid Dimensions Configuration

**📸 Grid Setup Command**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_1_grid_setup.png)
*Figure 1.2: Grid dimensions configured in Magic. The layout grid is adjusted to match the PDK track specifications.*

**Design Rule:**

> 📏 **Standard Cell Width:** To align with the manufacturing grid and routing resources, the width of standard cells must be an **ODD multiple of the X-pitch (0.46µm)**.

**Grid Configuration Command:**
To visualize this in the Magic layout tool, apply the following command:

```tcl
grid 0.46um 0.34um 0.23um 0.17um
```

| **Parameter** | **Value** | **Purpose** |
|:--------------|:---------:|:------------|
| X-pitch | `0.46µm` | Horizontal routing pitch definition |
| Y-pitch | `0.34µm` | Vertical routing pitch definition |
| X-offset | `0.23µm` | Horizontal grid origin offset (Half pitch) |
| Y-offset | `0.17µm` | Vertical grid origin offset (Half pitch) |

-----

## 🔌 Section 2: Port Definition & LEF Generation {#-section-2-port-definition--lef-generation}

### 2.1 Creating Ports in Magic

Ports serve as the electrical interface for the standard cell. Defining them correctly is crucial for the place-and-route tool to recognize connectivity.

**Port Definition Process:**

1.  Select the specific geometry (metal layer) intended for the port.
2.  Navigate to **Edit → Text** to open the text dialog.
3.  specific the port name and define its attributes.

**📸 Port Creation Interface**

Follow this GitHub: [https://github.com/user-attachments/assets/ede1d061-b743-4fcd-9cfe-96a201fea458](https://github.com/user-attachments/assets/ede1d061-b743-4fcd-9cfe-96a201fea458)

-----

### 2.2 Port Attributes Configuration

Proper port properties ensure the extraction tool correctly identifies the pin's function and direction.

**Key Port Properties:**

  - **Port Class**: Defines direction (Input, Output, Inout).
  - **Port Use**: Defines function (Signal, Power, Ground, Clock).
  - **Layer Attachment**: Specifies the metal layer associated with the port.
  - **Port Name**: Standard identifiers (e.g., A, Y, VPWR, VGND).

**📸 Port Class and Use Settings**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_2_port.png)
*Figure 2.1: Port attribute configuration showing port class (input/output) and port use (signal/power) settings.*

-----

### 2.3 Saving the Layout and Generating LEF

Once the physical layout and ports are defined, the cell must be saved and exported as a LEF file. The LEF file contains only the boundary, pin positions, and metal blockage information required for placement and routing.

**Save Command:**

```tcl
save sky130_vsdinv.mag
```

**Generate LEF Command:**

```tcl
lef write
```

**📸 LEF Write Command Execution**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_3_lef_write.png)
*Figure 2.2: Execution of the 'lef write' command, creating the abstract view of the cell.*

-----

### 2.4 Viewing Generated LEF File

**Commands to Verify LEF Content:**

```bash
cd vsdstdcelldesign
less sky130_vsdinv.lef
```

**📸 Opening LEF File**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_4_open_lef.png)
*Figure 2.3: Contents of sky130\_vsdinv.lef displaying the MACRO definition, pin coordinates (A, Y, VPWR, VGND), and obstruction layers.*

**LEF File Structure:**

```
MACRO sky130_vsdinv
  - CLASS CORE          ; Defines cell type
  - SIZE (width) BY (height)
  - PIN definitions     ; Includes direction and layer
  - OBS                 ; Obstruction (blockage) layers
END sky130_vsdinv
```

-----

## 🔗 Section 3: Custom Cell Integration into OpenLane {#-section-3-custom-cell-integration-into-openlane}


### 3.1 Source Directory Contents

**📸 Source Folder with LEF and Libraries**

**Required Files for Custom Flow:**

  - ✅ `sky130_vsdinv.lef` - Custom cell abstract view.
  - ✅ `sky130_fd_sc_hd__slow.lib` - Slow corner library for Max delay analysis (Setup).
  - ✅ `sky130_fd_sc_hd__typical.lib` - Typical corner library for nominal analysis.
  - ✅ `sky130_fd_sc_hd__fast.lib` - Fast corner library for Min delay analysis (Hold).

**📸 Library Files Listing**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_5_file_setup.png)

*Figure 3.2: Detailed view of all liberty (.lib) and LEF files available in the source directory.*

-----

### 3.2 Modifying config.tcl

The `config.tcl` file must be updated to point to the new library files and explicitly include the custom LEF.

**Configuration Updates:**

```tcl
# Design Definition
set ::env(DESIGN_NAME) "picorv32a"

set ::env(VERILOG_FILES) "./designs/picorv32a/src/picorv32a.v"
set ::env(SDC_FILE) "./designs/picorv32a/src/picorv32a.sdc"

set ::env(CLOCK_PERIOD) "5.000"
set ::env(CLOCK_PORT) "clk"

set ::env(CLOCK_NET) $::env(CLOCK_PORT)

# Library Definitions
set ::env(LIB_SYNTH) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"
set ::env(LIB_FASTEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib"
set ::env(LIB_SLOWEST) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib"
set ::env(LIB_TYPICAL) "$::env(OPENLANE_ROOT)/designs/picorv32a/src/sky130_fd_sc_hd__typical.lib"

# Include Custom LEF
set ::env(EXTRA_LEFS) [glob $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/src/*.lef]

set filename $::env(OPENLANE_ROOT)/designs/$::env(DESIGN_NAME)/$::env(PDK)_$::env(STD_CELL_LIBRARY)_config.tcl
if { [file exists $filename] == 1} {
	source $filename
}
```


### 3.3 Design Preparation with Overwrite

Prepare the OpenLane environment. The `-overwrite` flag is crucial here to ensure the flow recognizes the changes made to the configuration file.

**Prep Command:**

```tcl
% prep -design picorv32a -tag RUN_2025.11.13_10.53.33 -overwrite
```

> 💡 **Note:** The `-overwrite` flag forces the removal of previous run data for this tag and re-initializes the configuration.

**📸 Preparation Complete**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_6_prep.png)
*Figure 3.4: Design successfully prepared with updated configuration values.*

-----

### 3.5 Adding Custom LEF to Merged LEF

OpenLane typically merges all standard cell LEFs into a single file. We must manually ensure our custom LEF is included in this merge step.

**Additional Commands:**

```tcl
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs
```

**📸 LEF Merging Process**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_7_addlef_merge.png)
*Figure 3.5: Execution of commands to add custom LEF files to the system's merged LEF database.*

-----

### 3.6 Running Synthesis with Custom Cell

With the custom cell integrated, run the synthesis step. The synthesizer should now map logic to the custom inverter if it provides better area or timing characteristics.

**Synthesis Command:**

```tcl
run_synthesis
```

**📸 Custom Inverter Mapped in Synthesis**

**📸 Instance Count of Custom Cell**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_8_customcellintegration.png)
*Figure 3.6: Synthesis statistics confirming the instantiation of sky130\_vsdinv cells in the netlist.*

-----

### 3.7 Initial Synthesis Results

**📸 Synthesis Completion with Timing Metrics**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_9_tns_wns.png)
*Figure 3.7: Initial synthesis results showing WNS (Worst Negative Slack) and TNS (Total Negative Slack). Both values indicate significant timing violations.*

**Timing Metrics:**

```
tns -120.47
wns -1.21
```

| **Metric** | **Value** | **Status** |
|:-----------|:---------:|:-----------|
| **WNS** (Worst Negative Slack) | `-1.21 ns` | ⚠️ Critical Violation |
| **TNS** (Total Negative Slack) | `-120.47 ns` | ⚠️ Critical Violation |

⚠️ **Analysis**: The design currently fails timing requirements significantly. Optimization is mandatory.

-----

## ⚡ Section 4: Timing Analysis & Optimization {#-section-4-timing-analysis--optimization}

### 4.1 Synthesis Optimization Variables

To address timing violations, we must tune the synthesis parameters in OpenLane.

**Key OpenLane Synthesis Variables:**

| **Variable** | **Description** | **Default Value** |
|:-------------|:----------------|:------------------|
| `SYNTH_STRATEGY` | Synthesis optimization focus.<br>**Options:** `DELAY 0-3` (Timing focus) / `AREA 0-2` (Area focus) | `AREA 0` |
| `SYNTH_BUFFERING` | Enables buffer insertion for high fanout/long nets | `1` (Enabled) |
| `SYNTH_SIZING` | Enables cell sizing (replacing cells with higher drive strength) | `0` (Disabled) |
| `SYNTH_MAX_FANOUT` | Maximum fanout limit before buffering occurs | `5` |
| `SYNTH_MAX_TRAN` | Max transition time (slew) allowed | `10%` of clock period |
| `SYNTH_DRIVING_CELL` | Cell used to drive input ports during analysis | `sky130_fd_sc_hd__inv_8` |
| `SYNTH_CAP_LOAD` | Capacitive load assumption on output ports | `17.65` fF |
| `IO_PCT` | Percentage of clock period reserved for I/O delays | `0.2` (20%) |

**Library Configuration:**

| **Variable** | **Purpose** | **Default Path** |
|:-------------|:------------|:-----------------|
| `LIB_SYNTH` | Library used for logic mapping | `sky130_fd_sc_hd__tt_025C_1v80.lib` |
| `LIB_SLOWEST` | Worst-case corner (Max Delay) | `sky130_fd_sc_hd__ss_100C_1v60.lib` |
| `LIB_FASTEST` | Best-case corner (Min Delay) | `sky130_fd_sc_hd__ff_n40C_1v95.lib` |

**Clock Tree Configuration:**

| **Variable** | **Description** | **Default** |
|:-------------|:----------------|:------------|
| `CLOCK_BUFFER_FANOUT` | Target fanout for clock tree buffers | `16` |
| `ROOT_CLK_BUFFER` | Buffer used at the root of the clock tree | `sky130_fd_sc_hd__clkbuf_16` |
| `CLK_BUFFER` | Buffers used for internal clock nodes | `sky130_fd_sc_hd__clkbuf_4` |

-----

### 4.2 Timing Optimization Strategy

We apply an aggressive timing optimization strategy by prioritizing delay reduction over area and strictly limiting fanout.

**Optimization Commands:**

```tcl
package require openlane 0.9
prep -design picorv32a -tag 31-10_05-39 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs

# Optimization Settings
set ::env(SYNTH_STRATEGY) "DELAY 3"   ;# Aggressive delay optimization
set ::env(SYNTH_SIZING) 1             ;# Enable cell sizing
set ::env(SYNTH_MAX_FANOUT) 4         ;# Reduce max fanout to 4

run_synthesis
```

**📸 Optimization Commands Execution**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_10_opt_syn.png)
*Figure 4.1: Configuring synthesis parameters for timing optimization using DELAY 3 strategy and enabled cell sizing.*

-----

### 4.3 Improved Timing Results

**📸 Slack Reduction Achieved**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_11_opt_time.png)
*Figure 4.2: Synthesis report following optimization. Slack values are significantly reduced, indicating improved timing closure proximity.*

✅ **Result**: Parameter tuning has successfully reduced the negative slack.

-----

### 4.4 Floorplan Execution

Following synthesis, we proceed to floorplanning.

**Floorplan Commands:**

```tcl
run_floorplan
```

**📸 Successful Floorplan Using Individual Steps**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_12_floorplan.png)
*Figure 4.4: Floorplan completion*

✅ **Status**: Floorplan completed successfully.

-----

### 4.6 Placement Execution

**Placement Command:**

```tcl
run_placement
```

**📸 Placement Process Running**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_13_placement.png)
*Figure 4.5: Execution of the placement stage, covering global placement and detailed placement with overflow optimization.*

-----

### 4.7 Viewing Placement in Magic

To visually verify the placement of standard cells and the custom inverter, we load the DEF file into Magic.

**Magic Command:**

```bash
magic -T ~/./sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```



**📸 Placement View in Magic**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_14_placement_view_magic.png)
*Figure 4.7: Full placement layout view showing standard cells arranged in rows across the core area.*

-----

### 4.8 Locating Custom Inverter in Layout

**Finding Cell:**
Use the `what` command in the tkcon window after selecting a cell to identify it.


-----

### 4.9 Expanding Custom Cell

**Expand Command:**
To view the internal geometry of the abstract view (if GDS is loaded) or the LEF boundaries:

```tcl
expand
```

**📸 Expanded Internal View**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_15_view_expand.png)
*Figure 4.9: Expanded view of the custom inverter showing internal metal layers and connectivity.*

-----

### 4.10 Pre-Layout Static Timing Analysis (STA) Configuration

Before proceeding to routing, strict timing verification is required. We utilize OpenSTA with a specific configuration file (`pre_sta.conf`).

**Creating pre\_sta.conf:**

**📸 pre\_sta.conf File**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_16_pre_sta_conf.png)
*Figure 4.10: STA configuration file specifying the process corner libraries, the synthesized Verilog netlist, and SDC constraints.*

**Configuration Contents:**

```tcl
set_cmd units -time ns -capacitance pF -current mA -voltage V -resistance kOhm -distance um
read_liberty -max /home/iraj/VLSI/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__slow.lib
read_liberty -min /home/iraj/VLSI/openlane_working_dir/openlane/designs/picorv32a/src/sky130_fd_sc_hd__fast.lib
read_verilog /home/iraj/VLSI/openlane_working_dir/openlane/designs/picorv32a/runs/30-10_15-28/results/synthesis/picorv32a.synthesis.v
link_design picorv32a
read_sdc /home/iraj/VLSI/openlane_working_dir/openlane/designs/picorv32a/src/my_base.sdc
report_checks -path_delay min_max -fields {slew trans net cap input_pin}
report_tns
report_wns
```

-----

### 4.11 SDC Constraints File

The Synopsys Design Constraints (SDC) file defines the timing requirements.

**📸 my\_base.sdc File**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_17_sdc.png)

*Figure 4.11: SDC file defining clock period, I/O delays, and driving cell characteristics.*

**Key Constraints:**

```tcl
set ::env(CLOCK_PORT) clk
set ::env(CLOCK_PERIOD) 12.0
set ::env(SYNTH_DRIVING_CELL) sky130_fd_sc_hd__inv_8
set ::env(SYNTH_DRIVING_CELL_PIN) Y
set ::env(SYNTH_CAP_LOAD) 17.6

create_clock [get_ports $::env(CLOCK_PORT)] -period $::env(CLOCK_PERIOD)

set IO_PCT 0.2
set input_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]
set output_delay_value [expr $::env(CLOCK_PERIOD) * $IO_PCT]

# Set I/O delays and Loads
set_input_delay $input_delay_value -clock [get_clocks $::env(CLOCK_PORT)] $all_inputs_wo_clk_rst
set_output_delay $output_delay_value -clock [get_clocks $::env(CLOCK_PORT)] [all_outputs]
set_load [expr $::env(SYNTH_CAP_LOAD) / 1000.0] [all_outputs]
```

-----

### 4.12 Execution of Pre-Layout STA

We execute OpenSTA to verify timing before physical effects are fully introduced.

**Command:**

```bash
sta pre_sta.conf
```

**📸 Initial Timing Report where slack is MET**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_18_sta.png)
*Figure 4.12: Initial STA report detailing setup violations, slack calculations, and path delays.*


-----

### 4.13 Synthesis Refinement for Timing

Observing negative slack, we refine the synthesis strategy to prioritize timing over area further.

**Refined Synthesis Variables:**

```tcl
set ::env(SYNTH_STRATEGY) "DELAY 3"      ;# Aggressive delay optimization
set ::env(SYNTH_SIZING) 1                ;# Enable cell sizing
set ::env(SYNTH_MAX_FANOUT) 4            ;# Strictly limit fanout
set ::env(SYNTH_BUFFERING) 1             ;# Enable buffer insertion
run_synthesis
```

**📸 Re-Synthesis Output After Optimization**

> **Result:** The synthesizer adjusted drive strengths and reduced fanout, resulting in improved timing metrics.

-----

### 4.14 Identifying & Fixing High-Delay Nets

Post-synthesis, we use STA to identify specific nets with high delays and manually upsize the driving cells. This is done using a script (`Test_sta.conf`) that detects high-delay instances and replaces them with higher drive-strength variants.

**Command:**

```bash
sta Test_sta.conf
```

### 4.15 Manual Optimization (Interactive Debugging)

For targeted fixes, manual commands can be issued within the STA environment:

```tcl
report_net -connections <net_name>
replace_cell <instance> <library>/<higher_drive_cell>
report_checks -from <start> -to <end> -through <instance>
```

> 💡 **Upsizing Concept**: Replacing a cell with a larger version reduces output resistance ($R_{out}$), allowing it to charge the load capacitance ($C_{load}$) faster, thereby reducing delay ($Delay \propto R_{out} \cdot C_{load}$).

-----

### 4.16 Writing the Modified Netlist

After achieving timing closure through upsizing, the modified netlist must be saved for downstream tools.

**Command:**

```bash
write_verilog /home/VLSI/OpenLane/designs/picorv32a/runs/31-10_05-39/results/synthesis/picorv32a.synthesis.v
```

✅ **Outcome:** The new netlist contains optimized drive strengths and is ready for Floorplanning and Placement.

-----

## 🏗️ Section 5: Floorplan to Post-CTS Timing {️#-section-5-floorplan-to-post-cts-timing}

### 5.1 Overview

This phase covers the transition from the optimized netlist to physical implementation, culminating in Clock Tree Synthesis (CTS) and post-CTS timing verification.

**Process Flow:**

```
┌─────────────────────┐
│ Synthesis Variables │ → DELAY 3, SIZING=1, MAX_FANOUT=4
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│    Floorplanning    │ → run_floorplan
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│     Placement       │ → run_placement (Global + Detailed)
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Clock Tree Synth   │ → run_cts (Build balanced tree)
└──────────┬──────────┘
           ▼
┌─────────────────────┐
│  Post-CTS Analysis  │ → OpenROAD timing + skew reports
└─────────────────────┘
```

-----

### 5.2 Clock Tree Synthesis (CTS)

CTS builds the distribution network for the clock signal.

**Command:**

```tcl
run_cts
```

**CTS Objectives:**

1.  ⚖️ **Minimize Skew:** Ensure clock arrival times are equal across all flip-flops.
2.  🔌 **Minimize Insertion Delay:** Reduce latency from source to destination.
3.  🌳 **Balanced Topology:** Construct a balanced tree (e.g., H-tree) to manage signal propagation.

**📸 CTS Start:**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_19_cts.png)
*Figure 5.3: Clock Tree Synthesis initiation.*

**📸 CTS Process:**

![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_20_cts_time.png)
*Figure 5.4: CTS execution showing buffer insertion and tree balancing.*

✅ **Status:** CTS completed successfully.

-----

### 5.5 Post-CTS Timing Analysis

Post-CTS analysis is critical because it uses **propagated clocks** (real delays) rather than ideal clocks.

**Step 1: Launch OpenROAD**

```tcl
openroad
```

**📸 Result:**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_21_postcts.png)
*Figure 5.5: OpenROAD interface launched for post-CTS analysis.*

**Step 2: Load Design & Generate Reports**

```tcl
read_lef /openLANE_flow/designs/picorv32a/runs/31-10_05-39/tmp/merged.lef
read_def /openLANE_flow/designs/picorv32a/runs/31-10_05-39/results/cts/picorv32a.cts.def
write_db pico_cts.db
read_db pico_cts.db
read_verilog /openLANE_flow/designs/picorv32a/runs/31-10_05-39/results/synthesis/picorv32a.synthesis_cts.v
read_liberty $::env(LIB_SYNTH_COMPLETE)
read_sdc /openLANE_flow/designs/picorv32a/src/my_base.sdc
set_propagated_clock [all_clocks]
report_checks -path_delay min_max -format full_clock_expanded -digits 4
report_checks -path_delay min_max -fields {slew trans net cap input_pins} -format full_clock_expanded
```

**📸 Detailed Timing Report:**
![image](https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/8c20d5b9ab2187b262928967ecb76f9dcb793cc5/week6/Day4/day4_images/day4_22_sta_postcts.png)
*Figure 5.7: Comprehensive post-CTS timing report showing setup and hold analysis.*

### Pre-CTS vs Post-CTS

| Aspect | Pre-CTS | Post-CTS |
|--------|---------|----------|
| **Clock Model** | Ideal (Zero delay/skew) | Propagated (Real delay) |
| **Clock Skew** | Not considered | ✅ Included in analysis |
| **Hold Analysis** | Invalid (Clock is ideal) | ✅ Valid and accurate |
| **Accuracy** | Optimistic | Realistic |

-----

## ⏱️ Setup & Hold Timing Fundamentals

### Timing Equations

```
Setup Check:  T_data_arrival + T_setup ≤ T_clock_arrival
Hold Check:   T_data_arrival ≥ T_clock_arrival + T_hold
```

### Timing Requirements

| Check | Requirement | Description |
|-------|-------------|-------------|
| **Setup** | Data must settle **before** clock edge | Determines Max Frequency |
| **Hold** | Data must remain stable **after** clock edge | Prevents data corruption |

**Success Criteria:** Both Setup Slack and Hold Slack must be positive.

-----

## 🎓 Interview-Ready Talking Points

### Q1: Explain the complete flow of the Day 4 lab.

**Answer:** "Day 4 focused on achieving timing closure through pre-layout analysis and physical implementation. We started by integrating a custom inverter cell into the OpenLane flow by generating LEF files and updating the configuration. We then ran synthesis with timing-focused strategies (DELAY 3). Using OpenSTA, we performed pre-layout timing analysis, identified high-delay nets, and optimized them by upsizing driving cells. Finally, we proceeded through floorplanning and placement to Clock Tree Synthesis (CTS). Post-CTS, we verified timing using propagated clocks in OpenROAD to ensure both setup and hold constraints were met."

### Q2: What is the importance of Clock Tree Synthesis (CTS)?

**Answer:** "CTS is crucial for minimizing clock skew, which is the difference in clock arrival times at different flip-flops. A balanced clock tree ensures the system operates synchronously. Furthermore, CTS inserts the actual buffers and wires for the clock, allowing for accurate post-layout timing analysis. Pre-CTS analysis uses ideal clocks and cannot reliably predict hold violations, whereas Post-CTS provides the real insertion delay and skew data necessary for sign-off."

### Q3: How did you achieve timing closure?

**Answer:** "Timing closure was achieved via a multi-tiered optimization approach. First, I adjusted global synthesis parameters, setting `SYNTH_STRATEGY` to 'DELAY 3' and enabling `SYNTH_SIZING`. Second, I constrained `SYNTH_MAX_FANOUT` to 4 to prevent excessive loading on nets. Finally, I performed targeted manual optimization in OpenSTA by identifying the worst negative slack paths and replacing the driving cells with higher drive-strength variants (upsizing). This combination converted negative slack into positive slack."

### Q4: What is cell upsizing and when is it used?

**Answer:** "Cell upsizing involves replacing a standard cell with a variant that has the same logic function but larger transistors (higher drive strength), such as replacing an `AND2_X1` with an `AND2_X4`. This lowers the output resistance, allowing the cell to drive capacitive loads faster, thus reducing delay. It is used specifically on critical timing paths where delay bottlenecks exist. The trade-off is a slight increase in area and power consumption."

-----

## 📚 Key Takeaways

1.  **Track-Based Design:** Standard cells must be designed with widths that are odd multiples of the track pitch to ensuring correct alignment with routing resources.
2.  **LEF Abstraction:** The LEF file allows tools to see the cell's boundary and pins without needing the complex internal details, facilitating hierarchical design.
3.  **Timing Optimization:** Effective optimization requires a mix of global synthesis settings (strategies, fanout limits) and local manual interventions (upsizing critical cells).
4.  **CTS Necessity:** A balanced clock tree is essential for minimizing skew and enabling accurate Hold time analysis.
5.  **Pre- vs. Post-CTS:** Pre-CTS allows for fast logic optimization using ideal clocks, while Post-CTS is required for final timing verification using real propagated clocks.

-----

**🎉 Day 4 Lab Successfully Completed\!**

