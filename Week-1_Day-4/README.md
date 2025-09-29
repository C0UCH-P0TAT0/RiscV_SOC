cat << 'EOF' > README_DAY_4.md
# 📘 Day 4: Verilog Assignments, Mismatches, and GLS

This guide covers the most critical rules in Verilog for ensuring your design works in silicon. We'll explore blocking vs. non-blocking assignments, the dangerous bugs that arise from misusing them, and how to verify your synthesized design with Gate-Level Simulation (GLS).

## 📜 Table of Contents

| Section | Topic                                                                        |
|:-------:|------------------------------------------------------------------------------|
|    1    | [Verilog's Two Assignments: Blocking vs. Non-blocking](#-verilogs-two-assignments-blocking--vs-non-blocking-) |
|    2    | [The Critical Bug: Synthesis-Simulation Mismatch](#-the-critical-bug-synthesis-simulation-mismatch) |
|    3    | [The Final Check: Gate-Level Simulation (GLS)](#-the-final-check-gate-level-simulation-gls) |
|    4    | [Lab Exercises](#-lab-exercises)                                             |

---
## 💡 Verilog's Two Assignments: Blocking (`=`) vs. Non-blocking (`<=`)
Understanding the difference between these two is the key to writing correct RTL code. They tell the simulator how to schedule events, and using the wrong one is the most common source of bugs for beginners.

### Blocking Assignments (`=`)
A **blocking assignment** (`=`) executes **in order**. The next line of code is "blocked" and cannot start until the current line is finished. The variable is updated immediately.
* **Analogy**: Following a recipe. You *must* complete Step 1 before you can begin Step 2.
* **Rule**: Use for **combinational logic** (in an `always @(*)` block).

### Non-blocking Assignments (`<=`)
A **non-blocking assignment** (`<=`) executes **all at once**. The simulator figures out the results of all the lines first, and then, at the very end of the time step, it updates all the variables simultaneously.
* **Analogy**: Taking a snapshot. All events are captured at the same instant and then developed together.
* **Rule**: Use for **sequential logic** (in an `always @(posedge clk)` block), because this is how real flip-flops in hardware behave—they all update together on the clock edge.

---
## 💡 The Critical Bug: Synthesis-Simulation Mismatch
A **synthesis-simulation mismatch** is when your RTL simulation looks perfect, but the actual hardware produced by synthesis doesn't work the same way. This is a serious bug because your initial tests will pass, but the final chip will be broken.

The #1 cause of this bug is **using blocking assignments (`=`) to describe sequential logic**.

The simulator will follow the step-by-step blocking order, but the synthesis tool sees the code and builds parallel hardware (independent flip-flops). The simulated behavior and the real hardware are now different, creating a mismatch.

---
## 💡 The Final Check: Gate-Level Simulation (GLS)
**Gate-Level Simulation (GLS)** is a verification step performed *after* synthesis. Instead of simulating your high-level RTL blueprint, you simulate the detailed, gate-level netlist that the synthesis tool created.

Its primary purpose is to catch synthesis-simulation mismatches. By comparing the waveform from the original RTL simulation to the waveform from the GLS, you can prove that the synthesized circuit is functionally identical to your original design.

---
## ⚙️ Lab Exercises
Each lab in this section follows the same three-step workflow. The commands are shown once here for reference.

### Common Lab Workflow

**Step 1: RTL Simulation**
This step simulates the initial Verilog design file.

```bash
iverilog <design_name>.v <testbench_name>.v
./a.out
gtkwave <testbench_name>.vcd
```

**Step 2: Synthesis**
This step converts the Verilog design into a gate-level netlist using Yosys.

```bash
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
read_verilog <design_name>.v
synth -top <design_name>
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib 
write_verilog -noattr <design_name>_net.v
show
```

**Step 3: Gate-Level Simulation (GLS)**
This step simulates the synthesized netlist to verify its function against the original RTL.

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v <design_name>_net.v <testbench_name>.v
./a.out
gtkwave <testbench_name>.vcd
```

### Lab Results

**Part 1: A Correct Mux (`ternary_operator_mux`)**
This lab shows a correctly designed multiplexer. The RTL and GLS simulation results will match.
* **RTL Simulation Waveform**
    [ternary_operator_gtkwave](output_snapshots/ternary_operator_gtkwave.png)
* **Synthesis Output**
    [ternary_operator_synthesis](output_snapshots/ternary_operator_synthesis.png)
* **GLS Waveform (Matches RTL)**
    [ternary_operator_gls](output_snapshots/ternary_operator_gls.png)

***

**Part 2: A Mux with a Mismatch (`bad_mux`)**
This lab shows a poorly coded mux. The GLS waveform will *not* match the RTL waveform, revealing a bug.
* **RTL Simulation Waveform**
    [bad_mux_gtkwave](output_snapshots/bad_mux_gtkwave.png)
* **Synthesis Output**
    [bad_operator_synthesis](output_snapshots/bad_mux_synthesis.png)
* **GLS Waveform (Mismatch)**
    [bad_mux_gls](output_snapshots/bad_mux_gls.png)

***

**Part 3: A Sequential Mismatch (`blocking_caveat`)**
This lab shows the classic mismatch caused by using blocking (`=`) assignments for sequential logic. The RTL simulation looks fine, but the GLS reveals the hardware does not work as simulated.
* **RTL Simulation Waveform**
    [blocking_caveat_gtkwave](output_snapshots/blocking_caveat_gtkwave.png)
* **Synthesis Output**
    [blocking_caveat_synthesis](output_snapshots/blocking_caveat_synthesis.png)
* **GLS Waveform (Mismatch)**
    [blocking_caveat_gls](output_snapshots/blocking_caveat_gls.png)
EOF