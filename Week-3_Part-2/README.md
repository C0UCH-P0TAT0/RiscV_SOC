# ⚡ Static Timing Analysis (STA) & Digital Design Basics

## 📖 Table of Contents
| #  | Section |
|----|---------|
| 1  | [Objective](#objective) |
| 2  | [Types of Setup/Hold Paths](#types-of-setuphold-paths) |
| 3  | [Key Metrics in STA](#key-metrics-in-sta) |
| 4  | [Analysis Algorithms](#analysis-algorithms) |
| 5  | [Cell and Library Parameters](#cell-and-library-parameters) |
| 6  | [Clock-Related Effects](#clock-related-effects) |
| 7  | [Slew and Load Analysis](#slew-and-load-analysis) |
| 8  | [Clock Analysis](#clock-analysis) |
| 9  | [Latches and MOSFETs](#latches-and-mosfets) |
| 10 | [Timing Graphs (DAG) & Jitter](#timing-graphs-dag--jitter) |
| 11 | [Summary & Takeaways](#summary--takeaways) |

---

## 🎯 Objective
STA checks all timing paths in a digital circuit to ensure it works correctly at the intended clock frequency.  
Focus: **setup, hold, slack, critical paths, and clock effects**.

---

## 🧩 Types of Setup/Hold Paths

### 1. Register-to-Register (reg2reg)
- Data goes from **launch FF** → combinational logic → **capture FF**.  
- Ensures timing meets **setup/hold constraints**.

### 2. Input-to-Register (in2reg)
- Path from **primary input** → **register input**.  
- Ensures external signals are captured in **first clock cycle**.

### 3. Register-to-Output (reg2out)
- Path from **FF output** → **primary output**.  
- Determines how quickly valid data leaves the chip.

### 4. Input-to-Output (in2out)
- Purely combinational path from **input → output**.  
- No registers involved.

### 5. Clock Gating Checks
- Verifies **enable signal** timing for gated clocks.  
- Prevents glitches and short pulses.

### 6. Recovery / Removal Checks
| Check | Description |
|-------|------------|
| Recovery | Async signal must be **inactive before clock edge** (like setup) |
| Removal  | Async signal must remain **active after clock edge** (like hold) |

### 7. Data-to-Data Checks
- Ensures proper **timing between data signals**.  
- Avoids race conditions in protocols.

### 8. Latch Analysis
- Latches are **level-sensitive**.  
- Can **borrow time** from the next clock phase if data is late.

---

## 📈 Key Metrics in STA

### Arrival Time (AT)
Time for a signal to reach its destination.

### Required Time (RT)
Time by which data **must arrive**.

- Setup: `RT = Capture Edge + Clock Delay - Tsetup`  
- Hold: `RT = Capture Edge + Clock Delay + Thold`

### Slack
Timing margin between **required** and **actual arrival**.  

| Type | Formula | Meaning |
|------|--------|---------|
| Setup | RT − AT | Positive = OK, Negative = Violation |
| Hold  | AT − RT | Positive = OK, Negative = Violation |

---

## 🧮 Analysis Algorithms

| Type | Pros | Cons |
|------|------|------|
| Graph-Based (GBA) | Fast, efficient | Can be pessimistic |
| Path-Based (PBA) | Accurate | Slow, memory-heavy |

> Use GBA for initial paths, PBA for **critical paths**.

---

## 🔩 Cell and Library Parameters

- **Clk-to-Q Delay:** Time from clock edge → Q output.  
- **Setup/Hold Times:** Defined in **.lib files**.  
- Define **timing window** for safe data capture.

---

## 🕧 Clock-Related Effects

### Jitter
- Random variation in clock edges → reduces available data time.

### On-Chip Variation (OCV)
- Accounts for **process, voltage, temperature**.  
- Setup: launch slowed, capture sped up  
- Hold: launch sped up, capture slowed

### Clock Reconvergence Pessimism Removal (CRPR)
- Removes **double-counted pessimism** in common clock paths.

---

## ⚡ Slew and Load Analysis

### Slew (Transition Time)
- Speed of signal changing from 0 → 1 or 1 → 0.  
- Affects **gate delay**.

### Load
- **Fanout:** Number of gates driven by output → affects delay  
- **Capacitance:** Total load on gate → affects speed

---

## 🕰️ Clock Analysis

| Parameter | Impact |
|-----------|--------|
| Skew | Difference in arrival between flops |
| Positive Skew | Helps setup, hurts hold |
| Negative Skew | Helps hold, hurts setup |
| Pulse Width | Minimum clock high/low time |

---

## 🧠 Latches and MOSFETs

### Positive Latch
- Transparent when **CLK = HIGH**, holds when **CLK = LOW**  

### Negative Latch
- Transparent when **CLK = LOW**, holds when **CLK = HIGH**

### MOSFET Basics
- NMOS/PMOS controlled by **gate voltage**.  
- **Thin oxide** → better control, faster switching.

---

## 🕸️ Timing Graphs (DAG) & Jitter

### Directed Acyclic Graph (DAG)
- Nodes = gate pins  
- Edges = gate/wire delays  
- Used to **compute critical path** and **total delay**

### Jitter & Eye Diagram
- Jitter = small timing variations  
- Eye diagram shows **signal quality and stability**  

---

## 🧩 Summary & Takeaways
- STA checks **all timing paths** without full simulation.  
- Key concepts: **setup/hold, slack, skew, OCV, CRPR, slew, fanout**  
- GBA + PBA = speed + accuracy  
- DAG + metrics = find **critical paths**  
- Eye diagram + jitter = **signal quality check**

