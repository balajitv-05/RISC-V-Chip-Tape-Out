

---

# A 16-Mask CMOS Fabrication Process

This document details the step-by-step fabrication of a CMOS (Complementary Metal-Oxide-Semiconductor) device using a 16-mask process. Each section breaks down the function of the mask and the corresponding fabrication steps, enhanced with explanations of the **What**, **Why**, and **How** for each core concept.

## 16-Mask CMOS Fabrication Flowchart

```markdown
# 16-Mask CMOS Fabrication Flow (Text-Based)

**START: P-Type Substrate**
> **A. Wafer & Well Prep**
> 1.  **Mask 1: Define Active Region**
>     * `Process:` Etch $Si_3N_4$ (Silicon Nitride)
>     * `Process:` Grow Field Oxide (LOCOS Isolation)
>     * `Process:` Strip $Si_3N_4$
> 2.  **Mask 2: P-Well Implant (Boron)**
> 3.  **Mask 3: N-Well Implant (Phosphorous)**
>     * `Process:` Well Drive-in Diffusion (Furnace)
>
> **B. Transistor Formation**
> 4.  **Mask 4: NMOS $V_t$ Adjust Implant**
> 5.  **Mask 5: PMOS $V_t$ Adjust Implant**
>     * `Process:` Strip existing oxide
>     * `Process:` Grow new High-Quality Gate Oxide
>     * `Process:` Deposit Polysilicon
> 6.  **Mask 6: Pattern Polysilicon Gate**
>     * `Process:` Etch Polysilicon
> 7.  **Mask 7: NMOS LDD Implant (N-)**
> 8.  **Mask 8: PMOS LDD Implant (P-)**
>     * `Process (No Mask):` Form Side-wall Spacers
> 9.  **Mask 9: NMOS Source/Drain Implant (N+)**
> 10. **Mask 10: PMOS Source/Drain Implant (P+)**
>     * `Process:` Source/Drain Anneal (Furnace)
>
> **C. Contact & Metal Layer 1**
>     * `Process (No Mask):` Salicide ($TiSi_2$) Formation
> 11. **Mask 11: Pattern TiN Local Interconnect**
>     * `Process:` Deposit Insulator (BPSG)
>     * `Process (No Mask):` CMP (Planarization)
> 12. **Mask 12: Etch Contact Holes**
>     * `Process:` Deposit Tungsten
>     * `Process (No Mask):` CMP for Tungsten Plugs
>     * `Process:` Deposit Metal 1 (Aluminum)
> 13. **Mask 13: Pattern Metal 1**
>     * `Process:` Etch Metal 1
>
> **D. Interconnect Stack**
>     * `Process:` Deposit Dielectric ($SiO_2$)
>     * `Process (No Mask):` CMP (Planarization)
> 14. **Mask 14: Etch Via 1 Holes**
>     * `Process:` Deposit Tungsten
>     * `Process (No Mask):` CMP for Via 1 Plugs
>     * `Process:` Deposit Metal 2 (Aluminum)
> 15. **Mask 15: Pattern Metal 2**
>     * `Process:` Etch Metal 2
>
> **E. Final Steps**
>     * `Process:` Deposit Passivation Layer ($Si_3N_4$)
> 16. **Mask 16: Open Bond Pads**

**END: Fabrication Complete**
```
## Core Concepts in Fabrication

Before detailing the mask steps, here are two foundational processes used repeatedly.

### 1. Photolithography ("Litho")

* **What:** Photolithography is a process used to transfer a geometric pattern from a "mask" (or "reticle") onto the surface of a silicon wafer.
* **Why:** It is the fundamental method that allows for the selective processing (etching, implanting, or depositing material) of a chip. It's how microscopic circuits are "printed" with high precision.
* **How:** The process is a cycle:
    1.  **Coat:** The wafer is coated with a light-sensitive chemical called **photoresist**.
    2.  **Expose:** A mask blocks UV light, while the clear parts of the mask allow the light to pass through, exposing specific patterns of the photoresist.
    3.  **Develop:** A chemical developer removes either the exposed resist (positive resist) or the unexposed resist (negative resist), leaving a patterned stencil on the wafer.
    4.  **Process:** The wafer undergoes the main fabrication step (e.g., etching, ion implantation). The remaining photoresist protects the areas that are *not* to be processed.
    5.  **Strip:** The remaining photoresist is chemically stripped, leaving the patterned wafer ready for the next cycle.

### 2. Etching

* **What:** Etching is the process of using chemicals (wet etch) or plasma (dry etch) to remove material from the wafer surface.
* **Why:** It is used to sculpt the material layers (like polysilicon or oxide) into the desired patterns, based on the stencil created by photolithography.
* **How:**
    * **Wet Etch:** The wafer is submerged in a chemical bath (e.g., hydrofluoric acid for $SiO_2$) that dissolves the target material.
    * **Dry (Plasma) Etch:** The wafer is placed in a vacuum chamber where gases are energized into a plasma. This plasma bombards the wafer, removing material. **Anisotropic etching** is a key type of dry etch that removes material vertically much faster than horizontally, creating straight, vertical sidewalls.

---

## Mask 1: Active Region and LOCOS Isolation

We begin with a P-type substrate. The first step is to define the "active regions" (where transistors will be built) and electrically isolate them from each other.

### Concept: LOCOS (Local Oxidation of Silicon)

* **What:** LOCOS is an isolation technique that grows thick silicon dioxide ($SiO_2$), also called "field oxide," in specific areas of the wafer.
* **Why:** To electrically isolate individual transistors. Without this thick oxide, current could leak between adjacent devices, causing short circuits and device failure.
* **How:** This step uses a "reverse" mask. The $Si_3N_4$ (Silicon Nitride) layer acts as a hard mask that *prevents* oxidation. The $SiO_2$ (field oxide) is grown only in the areas where the $Si_3N_4$ was removed.

### Fabrication Steps

1.  A stack of materials is layered on the P-substrate: first ~40nm of $SiO_2$ (pad oxide), then ~80nm of $Si_3N_4$ (nitride mask), and finally ~1um of photoresist.
2.  **Photolithography (Mask 1):** Mask 1 is used to pattern the photoresist. The resist exposed to UV light is removed with a developing solution.
3.  **Etch:** The $Si_3N_4$ layer in the newly-exposed areas is etched away.
4.  **Strip:** The remaining photoresist is chemically removed. The $Si_3N_4$ now acts as a "hard mask."
5.  **Oxidation:** The wafer is placed in a high-temperature oxidation furnace. A thick "field oxide" (~500-1000nm) grows in the areas where the $Si_3N_4$ was removed. The $Si_3N_4$ layer blocks oxygen, preventing the "active regions" from oxidizing. This process is **LOCOS**.
6.  **Strip:** Finally, the $Si_3N_4$ mask layer is stripped off using hot phosphoric acid.




> **Note on "Bird's Beak":** A known side effect of LOCOS is the "Bird's beak." This is where the growing oxide diffuses laterally under the edge of the $Si_3N_4$ mask, creating a tapered edge. This feature consumes valuable active area and is a primary reason why modern processes (< 0.25um) use Shallow Trench Isolation (STI).

---

## Masks 2 & 3: N-Well and P-Well Formation

Next, we create the deep "wells" or "tubs" that will serve as the local substrate for the PMOS and NMOS transistors.

### Concept: Wells and Ion Implantation

* **What:** An **N-well** is a deep region of N-type doped silicon, and a **P-well** is a deep region of P-type doped silicon. This is a "Twin-Well" process, giving independent control over both transistor types.
* **Why:** CMOS (Complementary MOS) requires both PMOS and NMOS transistors.
    * An **NMOS** transistor must be built in a **P-type** body (the P-well).
    * A **PMOS** transistor must be built in an **N-type** body (the N-well).
* **How:**
    * **Ion Implantation:** High-energy, ionized atoms (dopants) are accelerated and "shot" at the wafer. The photoresist mask blocks the ions, allowing them to enter the silicon *only* in the patterned windows.
    * **Drive-in Diffusion:** After implantation, the wafer is heated in a furnace. This high temperature "activates" the dopants (allowing them to become part of the crystal lattice) and causes them to diffuse deeper into the substrate to form the final well.

### Fabrication Steps

1.  **Photolithography (Mask 2):** Using photoresist, Mask 2 patterns the wafer, opening windows over the P-well areas.
2.  **Implant (P-well):** A **Boron** (p-type) ion implant is performed to create the P-well.
3.  **Strip:** The photoresist is removed.
4.  **Photolithography (Mask 3):** The process is repeated with Mask 3, opening windows over the N-well areas.
5.  **Implant (N-well):** A **Phosphorous** (n-type) ion implant is performed to create the N-well.
6.  **Strip:** The photoresist is removed.
7.  **Drive-in:** The wafer is put into a high-temperature furnace. This "drive-in diffusion" step activates the dopants and allows them to diffuse deep into the substrate, forming the final wells.

---

## Masks 4 & 5: Gate Formation (Vt Adjust)

This step, also called the "channel implant," is *not* for building the gate itself, but for fine-tuning the transistor's properties *before* the gate is built.

### Concept: Threshold Voltage ($V_t$) Adjustment

* **What:** The **Threshold Voltage ($V_t$)** is the minimum gate-to-source voltage required to turn a transistor "on" (i.e., form an inversion channel).
* **Why:** The "natural" $V_t$ of a transistor based on the well doping alone is often not ideal for modern circuit design. This implant step "tunes" the $V_t$ to a precise value, controlling the transistor's switching speed and power consumption.
* **How:** A very low-dose ion implant is performed in the active regions. This implant is shallow and stays near the surface, where the transistor channel will form.

### Fabrication Steps

1.  **Photolithography (Mask 4):** Mask 4 is used to pattern resist, covering the N-well (PMOS area).
2.  **Implant (NMOS $V_t$):** A **Boron** (p-type) implant dopes the P-well's active region to set the $V_t$ for the future NMOS transistor.
3.  **Strip:** The photoresist is removed.
4.  **Photolithography (Mask 5):** A subsequent mask (implied Mask 5) is used to cover the P-well (NMOS area).
5.  **Implant (PMOS $V_t$):** An **Arsenic** (n-type) implant dopes the N-well's active region to set the $V_t$ for the future PMOS transistor.
6.  **Strip:** The photoresist is removed.

---

## Mask 6: Polysilicon Gate Formation

With the $V_t$ adjusted, the gate itself is built. This is one of the most critical steps in the process.

### Concept: High-Quality Gate Oxide & Polysilicon

* **What (Gate Oxide):** A very thin, ultra-pure layer of $SiO_2$ that electrically isolates the gate electrode from the silicon channel below.
* **Why (Gate Oxide):** This layer is the heart of the "MOS" transistor. Its properties control the transistor's performance. The previous oxide was damaged by implants, so a new, pristine, high-quality "gate oxide" must be grown (~10nm thin).
* **What (Polysilicon):** Polycrystalline silicon. It is used as the gate electrode material.
* **Why (Polysilicon):** Polysilicon is used instead of metal because it can withstand the very high temperatures of later steps (like S/D anneal) without melting or contaminating the device. It can also be doped to make it conductive.

### Fabrication Steps

1.  **Oxide Strip:** The existing oxide (damaged by $V_t$ implants) is etched away using a dilute hydrofluoric (HF) solution.
2.  **Gate Oxide Growth:** A new, high-quality, thin gate oxide is re-grown in a furnace.
3.  **Deposition:** A layer of ~0.4um polysilicon is deposited over the entire wafer.
4.  **Doping:** This polysilicon is given n-type ion implants (phosphorous or arsenic) to reduce its resistance and make it conductive.
5.  **Photolithography (Mask 6):** Mask 6 is applied to pattern photoresist, defining the shape of the gates.
6.  **Etch:** The unmasked polysilicon is etched away (typically with a dry anisotropic etch), leaving behind the final polysilicon gate structures.

---

## Masks 7 & 8: Lightly Doped Drain (LDD) Formation

This step is a reliability enhancement required for modern short-channel transistors.

### Concept: Lightly Doped Drains (LDD)

* **What:** Small, lightly doped regions (N- or P-) that are inserted between the main source/drain and the transistor channel.
* **Why:** To combat the **Hot Electron Effect**. In short transistors, the electric field near the drain is extremely high. This high field can accelerate electrons to very high energies ("hot electrons"). These electrons can slam into the gate oxide, causing damage and permanently shifting the transistor's $V_t$, leading to device failure.
* **How:** The LDD creates a "graded" junction that reduces the peak electric field, "cooling" the electrons and preventing damage. This implant uses the polysilicon gate itself as a mask.

### Fabrication Steps

1.  **Photolithography (Mask 7):** Mask 7 is used to cover the N-well (PMOS area).
2.  **Implant (NMOS LDD):** A light **N-implant** (Phosphorous) is performed. The polysilicon gate blocks the implant from the channel, so it only enters the silicon on either side, creating the "N-" LDD regions in the P-well.
3.  **Strip:** The photoresist is removed.
4.  **Photolithography (Mask 8):** A different mask (implied Mask 8) is used to cover the P-well (NMOS area).
5.  **Implant (PMOS LDD):** A light **P-implant** (Boron) is performed, creating the "P-" LDD regions in the N-well.
6.  **Strip:** The photoresist is removed.

---

## Side-wall Spacer Formation

To separate the LDD regions from the main source/drain, insulating spacers are built. **This step does not use a mask.**

### Concept: Anisotropic Etching

* **What:** Side-wall spacers are non-conductive (e.g., $Si_3N_4$ or $SiO_2$) structures formed on the vertical sides of the polysilicon gate.
* **Why:** They act as a "self-aligned" mask. They cover the LDD regions, allowing the next heavy implant to land further away from the gate. This ensures the heavily doped S/D region doesn't overlap the LDD, completing the graded junction.
* **How:** This step uses **anisotropic plasma etching**.
    1.  A thin layer (~0.1um) of $Si_3N_4$ or $SiO_2$ is deposited over the *entire* structure (this is a "conformal" layer).
    2.  A plasma anisotropic etch is performed. This etch removes material from horizontal surfaces (like the top of the gate and the active area) much faster than from vertical surfaces.
    3.  The result is that the horizontal insulator is etched away, but the material on the vertical "sidewalls" of the gate remains, forming spacers.

---

## Masks 9 & 10: Source and Drain Formation

Now the main, heavily doped (N+ and P+) source and drain (S/D) regions are created.

### Concept: Self-Aligned S/D Implantation

* **What:** The final, highly conductive regions of the transistor that act as the terminals for current.
* **Why:** These regions must have very low resistance to allow maximum current to flow when the transistor is "on." The LDD regions are too resistive for this.
* **How:** A very heavy ion implant is performed. The implant is "self-aligned" because the polysilicon gate *and* the side-wall spacers act as the mask, ensuring the N+/P+ dopants land precisely where needed, just outside the LDD regions.

### Fabrication Steps

1.  **Oxide Growth:** A thin "screen oxide" is grown to prevent the high-energy implant from damaging the silicon crystal (a phenomenon called "channeling").
2.  **Photolithography (Mask 9):** Mask 9 is used to cover the N-well (PMOS area).
3.  **Implant (NMOS S/D):** A heavy **N+ implant** (Arsenic) is performed, forming the NMOS source and drain in the P-well.
4.  **Strip:** The photoresist is removed.
5.  **Photolithography (Mask 10):** Mask 10 is used to cover the P-well (NMOS area).
6.  **Implant (PMOS S/D):** A heavy **P+ implant** (Boron) is performed, forming the PMOS source and drain in the N-well.
7.  **Strip:** The photoresist is removed.
8.  **Annealing:** The wafer goes into a high-temperature furnace. This **annealing** step is critical to:
    * Activate all the S/D dopants.
    * Repair the crystal lattice damage caused by the heavy implants.

---

## Mask 11: Salicide and Local Interconnects

This step ("salicide") is crucial for reducing the resistance of the gate and S/D regions to improve chip speed.

### Concept: Salicide (Self-Aligned Silicide)

* **What:** A process that forms a highly conductive metal-silicon alloy (a **silicide**, e.g., $TiSi_2$) on all exposed silicon surfaces simultaneously.
* **Why:** Even heavily-doped polysilicon and S/D regions have significant resistance. This resistance limits how fast the transistor can switch. The $TiSi_2$ layer acts as a "metal shunt," dramatically lowering the resistance of the gate, source, and drain, thus increasing device speed.
* **How:**
    1.  The thin screen oxide is etched away with an HF solution.
    2.  A layer of **Titanium (Ti)** is deposited over the *entire* wafer surface using **sputtering**.
    3.  The wafer is heated (annealed) in an $N_2$ (Nitrogen) ambient (650-700°C).
    4.  The Ti reacts *only* where it touches silicon (the top of the gate and the S/D regions), forming low-resistance **$TiSi_2$**.
    5.  Where Ti touched oxide (like the LOCOS or spacers), it reacts with the nitrogen ambient to form **$TiN$ (Titanium Nitride)**.
    6.  **Photolithography (Mask 11):** This mask is used to pattern the $TiN$ layer.
    7.  **Etch:** The unmasked $TiN$ is etched away (e.g., with RCA cleaning), leaving $TiN$ as a local interconnect, while the $TiSi_2$ remains on the gate/S/D.

---

## Mask 12: Contact Formation (Plugs)

This process creates the first set of vertical connections, linking the transistors (on the silicon layer) up to the first metal layer.

### Concept: CMP and Tungsten Plugs

* **What (Contact):** A vertical "plug" that connects the first metal layer (M1) down to the transistor's silicide (salicide) regions.
* **What (CMP):** Chemical Mechanical Polishing (or Planarization). A process that flattens the wafer using a combination of a chemical slurry and a physical polishing pad.
* **Why (CMP):** The fabrication process (LOCOS, gates, etc.) creates a "lumpy" topography. It is impossible to reliably print the patterns for the metal wires on an un-flat surface. A thick oxide (BPSG) is deposited, and CMP is used to polish it perfectly flat, creating a "foundation" for the wiring.
* **Why (Tungsten Plugs):** Aluminum (used for wires) is very poor at filling the deep, narrow holes (high "aspect ratio") etched for contacts. **Tungsten (W)**, deposited by CVD, is excellent at this.

### Fabrication Steps

1.  **Deposition (Insulator):** A thick (~1um) insulating layer of $SiO_2$ (doped with phosphorous/boron, known as **BPSG**) is deposited.
2.  **Planarization (CMP):** CMP is used to create a perfectly flat, planar surface.
3.  **Photolithography (Mask 12):** Mask 12 is used to pattern and etch "contact holes" down to the $TiSi_2$ on the gate and S/D regions.
4.  **Deposition (Liner):** A thin (~10nm) $TiN$ liner is deposited. This acts as a barrier (to prevent W from "poisoning" the silicon) and an adhesion layer.
5.  **Deposition (Tungsten):** A "blanket" Tungsten (W) layer is deposited, completely filling the contact holes (and covering the surface).
6.  **Planarization (CMP):** CMP is used a second time to polish the surface. This removes all Tungsten *except* for what is inside the contact holes, leaving "Tungsten plugs."

---

## Mask 13: Metal 1 (M1) Formation

This is the first layer of horizontal wiring, which connects the transistors together to form logic gates and circuits.

### Concept: Interconnects (Subtractive Etch)

* **What:** The first layer of metal (e.g., Aluminum) wiring that routes signals between different transistor contacts.
* **Why:** To build circuits. For example, connecting the drain of an NMOS and PMOS together (to form an inverter output) and connecting their gates together (to form the inverter input).
* **How (Subtractive Process):**
    1.  An **Aluminum (Al)** layer is deposited over the entire wafer, connecting to the Tungsten plugs.
    2.  **Photolithography (Mask 13):** Photoresist is applied, and Mask 13 is used to define the pattern for the M1 wires.
    3.  **Etch:** The unmasked Aluminum is **plasma etched** away, leaving the desired metal traces.
    4.  A final $SiO_2$ (dielectric) layer is deposited on top, and CMP is performed to planarize the surface, making it ready for the next layer.

---

## Mask 14: Via 1 Formation

After M1 is created, we need a way to connect it vertically to the *next* metal layer (Metal 2). This is done by creating "vias."

### Concept: Vias

* **What:** Vias are vertical metal plugs, identical in concept to "contacts," but they connect one metal layer to another metal layer (e.g., M1 to M2).
* **Why:** To allow signals to be routed in 3D. A complex chip cannot be wired on a single 2D plane.
* **How:** The process is identical to Mask 12 (Contact Formation):
    1.  **Photolithography (Mask 14):** Mask 14 is used to pattern and etch holes (vias) through the $SiO_2$ layer, stopping on the M1 layer below.
    2.  **Deposition:** A $TiN$ liner and Tungsten (W) are deposited to fill these holes.
    3.  **Planarization (CMP):** CMP is used to remove the excess metal, leaving only the "Via 1" plugs.

---

## Mask 15: Metal 2 (M2) Formation

Now, we create the second horizontal wiring layer, M2, which will sit on top of the $SiO_2$ and Via 1 plugs.

### Fabrication Steps

1.  A layer of **Aluminum (Al)** is deposited over the entire wafer, contacting the Via 1 plugs.
2.  **Photolithography (Mask 15):** Mask 15 is used to pattern the photoresist, defining the traces for M2.
3.  **Etch:** The unmasked Aluminum is plasma etched away, leaving the M2 wires. These wires make contact with the Via 1 plugs, connecting them to M1.
4.  Another layer of $SiO_2$ is deposited and planarized, preparing the wafer for the next layer (e.g., Via 2, M3).

> **Note:** This two-step process (Mask 14 for Vias, Mask 15 for Metal) is repeated as needed to create M3, M4, and so on. A 16-mask process is a basic example; modern complex chips can have over 100 masks to create many metal layers.

---

## Mask 16: Passivation and Bond Pad Opening

This is the final mask, used to protect the chip and allow it to be connected to the outside world.

### Concept: Passivation

* **What:** The **passivation layer** (often Silicon Nitride, $Si_3N_4$) is the final, thick, and durable protective layer deposited over the entire chip.
* **Why:** It seals the chip and protects its sensitive circuits from moisture, chemicals, and physical damage (like scratches) that can occur during wafer dicing and final packaging.
* **How:**
    1.  A final, thick passivation layer is deposited.
    2.  **Photolithography (Mask 16):** The final mask is used to pattern photoresist, opening windows *only* over the large "bond pads" (which are typically on the top metal layer).
    3.  **Etch:** This final etch removes the passivation layer from the bond pads, exposing the metal.
    4.  This exposure allows tiny gold or copper wires to be bonded from the chip's pads to the pins of its external package.

And that completes the fabrication! The wafer is then tested, diced into individual "die" (chips), and sent for packaging.
