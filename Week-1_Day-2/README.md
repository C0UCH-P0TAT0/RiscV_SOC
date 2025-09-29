# 📘 Day 2: Synthesis, Timing & Flop Design

This guide covers the key topics for Day 2: understanding timing files, comparing different ways to synthesize a design, and exploring how to code flip-flops efficiently.

## 📜 Table of Contents

| Section | Topic                                                                          |
|:-------:|--------------------------------------------------------------------------------|
|    1    | [The Parts Catalog: Timing Libraries (`.lib`)](#-the-parts-catalog-timing-libraries-lib) |
|    2    | [Building the Chip: Flat vs. Hierarchical Synthesis](#️-building-the-chip-flat-vs-hierarchical-synthesis) |
|    3    | [The Chip's Memory: Synchronous vs. Asynchronous Flops](#-the-chips-memory-synchronous-vs-asynchronous-flops) |
|    4    | [Lab Exercises](#-lab-exercises)                                               |

---
## 💡 The Parts Catalog: Timing Libraries (`.lib`)
A **timing library (`.lib`) file** is like a detailed data sheet or a "parts catalog" for all the tiny, pre-designed components (called **standard cells**) that can be used to build a chip. This file is provided by the chip manufacturer (the foundry).

The synthesis tool reads this file to learn everything about each component, including:
* **Speed**: How fast a gate is (its delay).
* **Power**: How much power it uses.
* **Size**: How much space it takes up on the chip.

This information is provided for different operating conditions (**PVT**: **P**rocess, **V**oltage, **T**emperature) to make sure the chip will work reliably whether it's hot, cold, or has slight manufacturing variations.

---
## 💡 Building the Chip: Flat vs. Hierarchical Synthesis
When turning RTL code into a circuit of gates, there are two main strategies: flat and hierarchical synthesis.

### Flat Synthesis
This method takes the entire design, throws all the parts into one giant digital pile, and optimizes everything at once.
* **Analogy**: Building a car by dumping all its individual nuts, bolts, and panels in one place and assembling it from scratch.
* **Pro**: Can produce the best possible result (in speed and size) because it can make optimizations across the whole design.
* **Con**: Extremely slow and uses a massive amount of computer memory. It's not practical for the huge, complex chips made today.

### Hierarchical Synthesis
This method builds the design in pieces, following the structure (modules) of the RTL code. It synthesizes each module separately and then connects them.
* **Analogy**: Building a car by first assembling the engine, then the chassis, and then the interior, and finally putting these finished parts together.
* **Pro**: Much faster, uses less memory, and allows large teams to work on different parts at the same time. This is the industry standard.
* **Con**: The final result might be slightly less optimized than a flat synthesis because changes are contained within each module.

---
## 💡 The Chip's Memory: Synchronous vs. Asynchronous Flops
Flip-flops are the basic memory elements that store data (a 1 or a 0). Flip-flops have an interesting feature: the reset (or set) input, which can be either synchronous or asynchronous.

### Synchronous Inputs
A **synchronous** input only affects the flip-flop on the "tick" of the clock (the active clock edge). It respects the timing of the system.

### Asynchronous Inputs
An **asynchronous** input affects the flip-flop instantly, at any time, ignoring the clock. It's an override command.
* **Asynchronous Reset**: Instantly forces the flip-flop's output to **0**.
* **Asynchronous Set**: Instantly forces the flip-flop's output to **1**.

---
## ⚙️ Lab Exercises

### Part 1: Exploring a Timing Library
1.  Navigate to the `lib` directory and view the contents of the timing library file to see how cell characteristics are defined.
    ```bash
    cd lib/
    vim sky130_fd_sc_hd__tt_025C_1v80.lib
    ```
[Library file](output_snapshots/lib_file.png)

### Part 2: Flat vs. Hierarchical Synthesis
In this lab, we use a design with multiple modules to compare the two synthesis methods.

**Hierarchical Synthesis (Default Method)**
1.  Launch Yosys.
    ```bash
    yosys
    ```

2.  Run the standard synthesis commands. The `synth` command by default preserves the hierarchy.
    ```bash
    read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    read_verilog multiple_modules.v
    synth -top multiple_modules 
    abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
    show multiple_modules
    ```

3.  Output (shows interconnected modules):
    [hierarchical synthesis](output_snapshots/heirarchical_synthesis.png)


**Flat Synthesis**
1.  Launch Yosys.
    ```bash
    yosys
    ```

2.  Run the synthesis commands, adding the `flatten` command to dissolve the hierarchy.
    ```bash
    read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    read_verilog multiple_modules.v
    synth -top multiple_modules 
    abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    flatten
    show 
    ```

3.  Output (shows one single, combined module):
    [flatten synthesis](output_snapshots/flatten_synthesis.png)


### Part 3: Simulating and Synthesizing Flip-Flops

**D-Flip-Flop with Synchronous Reset**
1.  Simulate the design to observe the reset behavior.
    ```bash
    iverilog dff_syncres.v tb_dff_syncres.v 
    ./a.out
    gtkwave tb_dff_syncres.vcd
    ```

2.  Waveform Output:
    [synchronous_gtkwave](output_snapshots/dff_synchronous_res_gtkwave.png)

3.  Synthesize the design in Yosys.
    ```bash
    yosys
    read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    read_verilog dff_syncres.v
    synth -top dff_syncres
    dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
    show
    ```
4.  Synthesis Output:
    [synchronous_synthesis](output_snapshots/dff_synchronous_res_synthesis.png)


**D-Flip-Flop with Asynchronous Reset**
1.  Simulate the design.
    ```bash
    iverilog dff_asyncres.v tb_dff_asyncres.v
    ./a.out
    gtkwave tb_dff_asyncres.vcd
    ```

2.  Waveform Output:
    [asynchronous_gtkwave](output_snapshots/dff_asynchronous_res_gtkwave.png)

3.  Synthesize the design.
    ```bash
    yosys
    read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    read_verilog dff_asyncres.v
    synth -top dff_asyncres
    dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
    show
    ```

4.  Synthesis Output:
    [synchronous_synthesis](output_snapshots/dff_asynchronous_res_synthesis.png)


**D-Flip-Flop with Asynchronous Set**
1.  Simulate the design.
    ```bash
    iverilog dff_async_set.v tb_dff_async_set.v
    ./a.out
    gtkwave tb_dff_async_set.vcd
    ```

2.  Waveform Output:
    [asynchronous_set_gtkwave](output_snapshots/dff_asynchronous_set_gtkwave.png)

3.  Synthesize the design.
    ```bash
    yosys
    read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    read_verilog dff_async_set.v
    synth -top dff_async_set
    dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
    abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
    show
    ```

4.  Synthesis Output:
    [asynchronous_set_synthesis](output_snapshots/dff_asynchronous_set_synthesis.png)