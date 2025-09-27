# 📚 Day 3: Synthesis Optimizations — Combinational & Sequential

> *“Optimization isn’t magic — it’s math, logic, and tool intelligence working together.”*

This session dives deep into **synthesis optimizations** — how tools transform your RTL into efficient gate-level netlists. You’ll learn about **combinational optimizations** (direct & boolean), **sequential optimizations** (basic & advanced), and how to control them using Yosys commands like `opt_clean -purge`.

---

## 🎯 Session Objectives

By the end of this session, you will:
- Understand **combinational optimizations**: direct mapping vs boolean logic reduction.
- Learn **sequential optimizations**: constant propagation, state optimization, retiming, cloning.
- Know how to use **Yosys optimization commands** (`opt_clean`, `opt`, etc.).
- See real examples of how synthesis simplifies logic using dot graphs.
- Recognize when and why to apply specific optimizations.

---

## 📘 Part 1: Combinational Logic Optimizations

Synthesis tools optimize combinational logic in two main ways:

### 1. Direct Mapping Optimization
- Maps expressions directly to available standard cells.
- Example: `assign y = a & b;` → mapped to `sky130_fd_sc_hd__and2_0`

🖼️ **Image 7**: Shows `opt_check` module with 2-input AND gate → minimal cell usage.

![Direct Mapping: AND Gate](https://via.placeholder.com/600x300?text=Image+7+-+Direct+Mapping+AND+Gate)

> *Image 7: Simple AND gate mapped directly to `sky130_fd_sc_hd__and2_0` — no further optimization needed.*

---

### 2. Boolean Logic Optimization
- Applies Boolean algebra to reduce expression complexity.
- Example: `assign y = (~a & b) | a;` → simplifies to `y = a | b`

🖼️ **Image 9**: Shows `opt_check2` with OR gate + handwritten derivation:  
`y = ~a·b + a = a + b`

![Boolean Optimization: OR Gate](https://via.placeholder.com/600x300?text=Image+9+-+Boolean+Optimization+OR+Gate)

> *Image 9: Synthesis simplified `(~a & b) | a` to `a | b` — shown as OR gate and verified with Boolean algebra.*

---

## 🧩 Part 2: Sequential Logic Optimizations

Sequential optimizations target flip-flops, registers, and state machines.

### Basic Optimization: Sequential Constant Propagation
- If a register is assigned a constant value, synthesis may remove the register or replace it with a wire.
- Reduces area and power.

🖼️ **Image 10**: Shows `multiple_module_opt2` with inputs `a`, `b`, `c`, `d` feeding into intermediate signals `$a`, `$b`, `$c`, `$d`, then to `U1.y`, `U2.y`, `U3.y` → eventually output `y`.

![Sequential Constant Propagation](https://via.placeholder.com/600x300?text=Image+10+-+Sequential+Constant+Propagation)

> *Image 10: Sequential logic with multiple stages — synthesis may propagate constants through registers if possible.*

---

### Advanced Optimizations

#### 1. State Optimization
- Reduces number of states in FSMs by merging equivalent states.
- Improves area and timing.

#### 2. Retiming
- Moves registers across combinational logic to balance delays.
- Improves clock frequency without changing functionality.

#### 3. Sequential Logic Cloning
- Duplicates sequential elements to break critical paths.
- Increases area but improves timing.

>
---

## 🛠 Part 3: Key Optimization Commands in Yosys

### `opt` — General Optimization Pass
Runs basic optimizations:
```bash
yosys> opt
```
➡️ Simplifies logic, removes unused cells, merges equivalents.

### `opt_clean -purge` — Clean Up Unused Logic
Removes dangling wires, unused modules, and redundant cells.

```bash
yosys> opt_clean -purge
```
➡️ Essential before final netlist generation to avoid bloating.

### `show` — Visualize Netlist
Generates `.dot` graph for viewing in Dot Viewer.

```bash
yosys> show
```

---

## 🖼️ Hands-On Examples from Images

### Image 6: 3-Input AND Optimization
```verilog
assign y = a & b & c;
```
→ Mapped to `sky130_fd_sc_hd__and3_1` (3-input AND cell).

![3-Input AND Gate](https://via.placeholder.com/600x300?text=Image+6+-+3-Input+AND+Gate)

> *Image 6: Direct mapping of 3-input AND to single cell — efficient use of library.*

---

### Image 8: XOR Optimization
```verilog
assign y = a ^ b ^ c;
```
→ Mapped to `sky130_fd_sc_hd__xnor2_1` (XNOR gate) — likely due to internal inversion or logic restructuring.

![XOR to XNOR Mapping](https://via.placeholder.com/600x300?text=Image+8+-+XOR+to+XNOR+Mapping)

> *Image 8: Synthesis may map XOR to XNOR + inversion depending on cell availability and cost.*

---

## 📊 Optimization Summary Table

| Optimization Type       | Sub-Type                  | Purpose                                      | 
|--------------------------|----------------------------|----------------------------------------------|
| **Combinational**        | Direct Mapping             | Map to available cells                       | 
|                          | Boolean Logic Reduction    | Simplify expressions using Boolean algebra   |
| **Sequential**           | Constant Propagation       | Remove unnecessary registers                 | 
|                          | State Optimization         | Reduce FSM states                            | 
|                          | Retiming                   | Balance delays across registers              | 
|                          | Logic Cloning              | Duplicate logic to break paths               | 

---

## ✅ Best Practices

- Always run `opt_clean -purge` before finalizing netlist.
- Use `show` after each major pass to visualize changes.
- Prefer simple, readable RTL — synthesis tools handle complex optimizations better than you think!
- Don’t over-optimize early — let the tool do its job unless you have specific constraints.

---

## 💻 Tools & Commands Used

| Tool       | Purpose                                  |
|------------|------------------------------------------|
| **Yosys**  | Open-source synthesis tool               |
| **ABC**    | Technology mapper & logic optimizer      |


### Common Commands:
```bash
opt                # Basic optimization
opt_clean -purge   # Clean up unused logic
show               # Generate and view graph
abc                # Advanced technology mapping
```

---

