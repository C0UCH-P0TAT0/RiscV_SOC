# 📘 Day 1: RTL Design, Simulation & Synthesis Guide

| Section | Topic                                                 |
|:-------:|-------------------------------------------------------|
|    1    | [What is RTL Design?](#-what-is-rtl-design)           |
|    2    | [What is Logic Synthesis?](#-what-is-logic-synthesis) |
|    3    | [Tool Overview](#-tool-overview)                      |
|    4    | [Lab Steps](#-lab-steps)                              |

---
## 💡 What is RTL Design?
A modern computer chip has billions of tiny parts, making it impossible to connect them all by hand. So, designers use RTL (Register-Transfer Level) design.
Think of it as creating a high-level blueprint for the chip. Instead of drawing every single wire, the blueprint simply shows how information moves between registers and the logic circuits that process it. Designers write this blueprint using special coding languages like Verilog or VHDL.

## 💡 What is Logic Synthesis?
A blueprint isn't enough to start building; you need a detailed construction plan. Logic Synthesis is the process where a computer automatically turns the RTL blueprint into that detailed plan. The final plan is called a gate-level netlist. It's a giant wiring diagram showing exactly how to connect millions of tiny, pre-designed logic gates to build the final circuit.

To do its job, the synthesis tool needs three things from the designer:

1.  **The RTL Code**: The high-level blueprint.
2.  **A Technology Library**: A "parts catalog" of all the available logic gates.
3.  **Constraints**: The rules for the project, like "make it fast," "don't use too much space," or "use less power."

---
## 🛠️ Tool Overview

* **Icarus Verilog (`iverilog`) is used for Simulation**: It checks if your RTL design works as expected before you try to build it. It compiles and runs your Verilog code to simulate the circuit's behavior.

* **GTKWave is used for Waveform Viewing**: After running a simulation with `iverilog`, GTKWave is used to look at the results visually. It shows you waveforms, which are graphs of how the signals in your circuit change over time, helping you confirm it's working correctly.

* **Yosys is used for Synthesis**: This is the tool that performs the logic synthesis. It takes your Verilog RTL code and converts it into a gate-level netlist using a library of standard components (standard cells) from a Process Design Kit (PDK) like Sky130.

---
## ⚙️ Lab Steps

### Part 1: Simulation using iverilog and GTKWave
1.  Clone the repository and navigate to the correct folder:
    ```bash
    git clone [https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git](https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git)
    cd sky130RTLDesignAndSynthesisWorkshop/verilog_files/
    ```

2.  Compile the Verilog files with `iverilog`, run the simulation, and open the waveform in GTKWave:
    ```bash
    iverilog good_mux.v tb_good_mux.v
    ./a.out 
     gtkwave tb_good_mux.vcd
    ```

3.  Simulation Output in GTKWave:
    [GTK wave](output_snapshots/good_mux_gtkwave.png)

### Part 2: Synthesis using Yosys
1.  Launch Yosys:
    ```bash
    yosys
    ```

2.  Inside Yosys, run the following commands to read the standard cell library, read the Verilog design, perform synthesis, and map it to the library cells. The `show` command will display a diagram of the resulting circuit.
    ```bash
    read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
     read_verilog good_mux.v
     synth -top good_mux
     abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
     show
    ```

3.  Synthesis Output Diagram:
    [good_mux_synthesis](output_snapshots/good_mux_synthesis.png)

4.  To generate the final gate-level netlist file, use the `write_verilog` command. The second command lets you view the file.
    ```bash
    write_verilog -noattr good_mux_netlist.v
    !vim good_mux_netlist.v
    ```

5.  Output of the Netlist file:
    [good_mux_netlist](output_snapshots/good_mux_netlist.png)