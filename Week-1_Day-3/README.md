# 📘 Day 3: Logic Optimization Techniques

This guide covers the "magic" that synthesis tools perform to make a chip design better. We'll explore the automatic optimization techniques used to improve a circuit's Power, Performance, and Area (PPA).

## 📜 Table of Contents

| Section | Topic                                                                        |
|:-------:|------------------------------------------------------------------------------|
|    1    | [What is Logic Optimization?](#-what-is-logic-optimization)                  |
|    2    | [Tricks for Combinational Logic (No Memory)](#-tricks-for-combinational-logic-no-memory) |
|    3    | [Tricks for Sequential Logic (With Memory)](#-tricks-for-sequential-logic-with-memory) |
|    4    | [Lab Exercises](#-lab-exercises)                                             |

---
## 💡 What is Logic Optimization?
**Logic optimization** is how a synthesis tool automatically makes a circuit **smaller**, **faster**, and **more power-efficient** without changing its fundamental function. The main goal is to meet the project's targets for speed, power, and size.

---
## 💡 Tricks for Combinational Logic (No Memory)
This is about improving logic circuits whose outputs depend only on their current inputs.

1.  **Constant Propagation**: The tool simplifies logic connected to a constant value.
    * **Example**: An AND gate with one input always set to `0` will always output `0`. The tool removes the gate and connects the output directly to ground (logic 0).
2.  **Boolean Logic Simplification**: Uses math rules (Boolean algebra) to reduce the number of gates needed.
    * **Example**: The expression `(A AND B) OR (A AND NOT B)` is mathematically identical to just `A`. The tool makes this replacement, saving two AND gates and an OR gate.
3.  **Common Sub-expression Elimination**: If the exact same logic calculation is needed in multiple places, the tool builds it only once and shares the result. This saves area by avoiding duplication.

---
## 💡 Tricks for Sequential Logic (With Memory)
This involves optimizing circuits that have memory elements like flip-flops.

1.  **Retiming**: To make a circuit faster, the tool can move flip-flops across logic blocks.
    * **Analogy**: It's like moving workers on an assembly line to better balance the workload. This shortens the longest delay between any two workers, allowing the entire line to run faster.
2.  **Clock Gating**: A huge power-saving technique. If a section of the chip isn't being used, its clock signal is temporarily turned off. This stops the transistors from switching, saving a lot of power.
    * **Analogy**: It's like putting a part of the chip to sleep when it's not needed.
3.  **Logic Trimming (Dead Code Elimination)**: If the output of a flip-flop or logic block isn't connected to anything, it's useless. The tool identifies and deletes this "dead code" and any logic that was only used to drive it.

---
## ⚙️ Lab Exercises
### Part 1: Combinational Logic Optimization Labs
#### Common Synthesis Workflow
The following command sequence is used for the `opt_check` labs.

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog <design_name>.v
synth -top <design_name>
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
```

#### Lab Results (`opt_check` series)
* **`opt_check` Output:**
    [opt_check_synthesis](output_snapshots/opt_check.png)
* **`opt_check2` Output:**
    [opt_check2_synthesis](output_snapshots/opt_check2.png)
* **`opt_check3` Output:**
    [opt_check3_synthesis](output_snapshots/opt_check3.png)
* **`opt_check4` Output:**
    [opt_check4_synthesis](output_snapshots/opt_check4.png)

---
#### Common Flatten Workflow
The following command sequence is used for the `multiple_module_opt` labs.

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog <design_name>.v
synth -top <design_name>
flatten
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
```

#### Lab Results (`multiple_module_opt` series)
* **`multiple_module_opt` Output:**
    [multiple_module_opt_synthesis](output_snapshots/multiple_module_opt.png)
* **`multiple_module_opt2` Output:**
    [multiple_module_opt2_synthesis](output_snapshots/multiple_module_opt_2.png)

### Part 2: Sequential Logic Optimization Labs
#### Common Simulation & Synthesis Workflow
The `dff_const` labs use this two-step process.

**Step 1: Simulation**
```bash
iverilog <design_name>.v <testbench_name>.v
./a.out
gtkwave <testbench_name>.vcd
```

**Step 2: Synthesis**
```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
read_verilog <design_name>.v
synth -top <design_name>
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
```

#### Lab Results (`dff_const` series)
* **`dff_const` Outputs:**
    [const1_gtkwave](output_snapshots/dff_const1_gtkwave.png)
    [const1_synthesis](output_snapshots/dff_const1_synthesis.png)
    [const2_gtkwave](output_snapshots/dff_const2_gtkwave.png)
    [const2_synthesis](output_snapshots/dff_const2_synthesis.png)
    [const3_gtkwave](output_snapshots/dff_const3_gtkwave.png)
    [const3_synthesis](output_snapshots/dff_const3_synthesis.png)
    [const4_gtkwave](output_snapshots/dff_const4_gtkwave.png)
    [const4_synthesis](output_snapshots/dff_const4_synthesis.png)
    [const5_gtkwave](output_snapshots/dff_const5_gtkwave.png)
    [const5_synthesis](output_snapshots/dff_const5_synthesis.png)

*(The labs for `dff_const2`, `dff_const3`, `dff_const4`, and `dff_const5` follow the exact same simulation and synthesis pattern.)*

### Part 3: Unused Logic Removal Labs
#### Common Synthesis Workflow
The `counter_opt` labs use the following synthesis flow.

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog <design_name>.v
synth -top <design_name>
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
show
```

#### Lab Results (`counter_opt` series)
**`counter_opt` (Unused output bits)**
* **Output:**
    [counter_opt_design](output_snapshots/unused_counter_opt.png)
    [counter_opt_synthesis](output_snapshots/counter_opt_synthesis.png)

**`counter_opt2` (Modified output logic)**
1.  First, copy and edit the file to change the output logic.

```bash
cp counter_opt.v counter_opt2.v
vim counter_opt2.v
```
    *In the editor, change the line `assign q = count[0];` to `assign q = (count[2:0] == 3'b100);` and save.*

2.  Synthesize the modified design using the workflow above (`<design_name>` is `counter_opt2`, but `-top` is `counter_opt`).
* **Output:**
    [counter_opt_design](output_snapshots/unused_counter_opt2.png)
    [counter_opt2_synthesis](output_snapshots/counter_opt2_synthesis.png)
EOF