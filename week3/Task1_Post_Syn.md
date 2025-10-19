# GLS OF BABYSOC
## POST-SYNTHESIS SIMULATION

### Purpose of GLS:
Gate-Level Simulation is used to verify the functionality of a design after the synthesis process. Unlike behavioral or RTL (Register Transfer Level) simulations, which are performed at a higher level of abstraction, GLS works on the netlist generated post-synthesis. This netlist includes the actual gates and connections used to implement the design.

### Key Aspects of GLS for BabySoC:
1. **Verification with Timing Information:**
   - GLS is performed using Standard Delay Format (SDF) files to ensure timing correctness.
   - This checks if the SoC behaves as expected under real-world timing constraints.

2. **Design Validation Post-Synthesis:**
   - Confirms that the design's logical behavior remains correct after mapping it to the gate-level representation.
   - Ensures that the design is free from issues like metastability or glitches.

3. **Simulation Tools:**
   - Tools like Icarus Verilog or a similar simulator can be used for compiling and running the gate-level netlist.
   - Waveforms are typically analyzed using GTKWave.

4. **Importance for BabySoC:**
   - BabySoC consists of multiple modules like the RISC-V processor, PLL, and DAC. GLS ensures that these modules interact correctly and meet the timing requirements in the synthesized design.


Here is the step-by-step execution plan for running the  commands manually:
---
### **Step 1: Load the Top-Level Design and Supporting Modules**
```bash
yosys
```



Inside the Yosys shell, run:
```yosys
read_verilog ./src/module/vsdbabysoc.v
read_verilog -I ./src/include /home/ananya123/VSDBabySoCC/src/module/rvmyth.v

```

---

### **Step 2: Load the Liberty Files for Synthesis**
Inside the same Yosys shell, run:
```yosys
read_liberty -lib ./src/lib/avsdpll.lib
read_liberty -lib ./src/lib/avsddac.lib
read_liberty -lib ./src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

---

### **Step 3: Run Synthesis Targeting `vsdbabysoc`**
```yosys
synth -top vsdbabysoc
```

![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/statistics_synth.png)
![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/statistics_synth1.png)

---

### **Step 4: Map D Flip-Flops to Standard Cells**
```yosys
dfflibmap -liberty ./src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/dfflibmap.png)

---

### **Step 5: Perform Optimization and Technology Mapping**
```yosys
opt
abc -liberty  ./src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```

![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/opt.png)
![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/statistics_synth1.png)

---

### **Step 6: Perform Final Clean-Up and Renaming**
```yosys
flatten
setundef -zero
clean -purge
rename -enumerate
```

![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/flatten.png)
![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/setundef.png)


---

### **Step 7: Check Statistics**
```yosys
stat
```

![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/stat.png)

---

### **Step 8: Write the Synthesized Netlist**
```yosys
write_verilog -noattr vsdbabysoc.synth.v
```

---

## POST_SYNTHESIS SIMULATION AND WAVEFORMS
---

### **Step 1: Compile the Testbench**
Run the following `iverilog` command to compile the testbench:
```bash
iverilog -o /post_synth_sim/ -DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1  ./src/module/testbench.v
```
---
### **Step 2: Navigate to the Post-Synthesis Simulation Output Directory**
```bash
cd output/post_synth_sim/
```
---
### **Step 3: Run the Simulation**

```bash
./post_synth_sim.out
```
---
### **Step 4: View the Waveforms in GTKWave**

```bash
gtkwave post_synth_sim.vcd
```

![Alt Text] (https://github.com/balajitv-05/RISC-V-Chip-Tape-Out/blob/7f0dcb2ec3e1d4777e0220876108290574292c8b/week3/images/simulationGLS.png)

---
