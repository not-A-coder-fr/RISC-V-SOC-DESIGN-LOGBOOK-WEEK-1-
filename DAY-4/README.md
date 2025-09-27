

# 📚 Day 4: Gate Level Simulation (GLS) & Synthesis-Simulation Mismatches #

> *“Verify logic after synthesis. Catch mismatches before silicon.”*

This session dives into the critical verification step in digital design flow — **Gate Level Simulation (GLS)** — and explores common pitfalls that lead to **Synthesis-Simulation Mismatches**, which can cause functional failures in hardware.

---

## 🎯 Session Objectives

By the end of this session, you will:
- Understand what GLS is and why it’s essential.
- Learn how to run GLS using IVERILOG.
- Identify and debug common synthesis-simulation mismatches.
- Master the correct usage of blocking vs. non-blocking statements in Verilog.
- Gain hands-on experience with real-world examples and waveform debugging.

---

## 📘 Part 1: Introduction to Gate Level Simulation (GLS)

### What is GLS?
- Running simulation using the **netlist** (gate-level representation) as the Design Under Test (DUT).
- The netlist is logically equivalent to RTL code — same testbench should produce identical results.
- Ensures **logical correctness** post-synthesis.
- *(Timing validation requires delay-annotated GLS — outside scope of this session.)*

### GLS Flow Using IVERILOG
```plaintext
[RTL Design] → [Synthesis] → [Gate-Level Netlist]
       ↓
[Test Bench] → [IVERILOG Simulator] → [VCD File] → [GTKWave Visualization]
```
> 💡 Note: If gate-level models are delay-annotated, GLS can also validate timing.

### Handwritten Example: From RTL to Gates
The instructor demonstrated converting an RTL `assign y = (a & b) | c;` into its gate-level equivalent:
```verilog
and u1(.a(a), .b(b), .y(y1));
or  u2(.a(y1), .b(c), .y(y));
```

---

## ⚠️ Part 2: Synthesis-Simulation Mismatch

### Common Cause: Missing Sensitivity List
A classic error occurs when using `always @(sel)` instead of `always @(*)` in combinational logic.

#### Example: Multiplexer (`mux`)
```verilog
module mux (
    input i0, i1, sel,
    output reg y
);
    always @(sel) begin  // ❌ WRONG! Missing i0, i1
        if(sel) y = i1;
        else    y = i0;
    end
endmodule
```

➡️ **Problem**: Simulator only triggers on `sel`, but synthesis infers combinational logic dependent on all inputs → mismatch!

✅ **Fix**: Use `always @(*)` to auto-detect all signals used inside the block.

---

## 🧩 Part 3: Blocking vs Non-Blocking Statements in Verilog

### Inside `always` Blocks:
| Type              | Symbol | Behavior                          |
|-------------------|--------|-----------------------------------|
| Blocking          | `=`    | Sequential execution — order matters |
| Non-Blocking      | `<=`   | Parallel evaluation — RHS evaluated first |

#### Caveat: Blocking Statements in Sequential Logic
Example: Flip-flop with reset
```verilog
always @(posedge clk or posedge reset)
    if(reset) begin
        q0 = 1'b0;  // Blocking assignment
        q  = 1'b0;
    end else begin
        q0 = d;     // ❌ Problematic!
        q  = q0;
    end
```
➡️ **Issue**: In simulation, `q0` updates immediately, so `q` gets new `d` value — but in synthesis, both assignments happen simultaneously → `q` gets old `q0` value → mismatch!

✅ **Fix**: Use non-blocking assignments (`<=`) for sequential logic:
```verilog
q0 <= d;
q  <= q0;
```

---

## 🔍 Part 4: Real-World Debugging Examples

### Example 1: Ternary Operator Mismatch
RTL:
```verilog
always @(*)
    y = q0 & c;
    q0 = a | b;
```
➡️ **Simulation**: Uses old `q0` value for `y`.
➡️ **Synthesis**: Infers combinational logic where `q0` and `y` update simultaneously → mismatch.

✅ **Solution**: Reorder assignments or use temporary variables.

### Example 2: Blocking Assignment in Combinational Logic
```verilog
always @(*)
    y = q0 & c;
    q0 = a | b;
```
➡️ Same issue as above — simulator uses old `q0`, synthesis infers concurrent logic.

✅ **Solution**: Use non-blocking or restructure logic.

---

## 💻 Hands-On Lab: GLS with IVERILOG & GTKWave

### Steps Demonstrated:
1. **Synthesize** RTL code to generate gate-level netlist.
2. **Run GLS** using IVERILOG with testbench and netlist.
3. **Generate VCD** file for waveform visualization.
4. **Open in GTKWave** to compare simulation vs. synthesized behavior.

### Key Observations:
- Waveform mismatches clearly visible in GTKWave.
- Red circles highlight points of divergence between RTL and GLS waveforms.
- Annotations like “Synth - Sim Mismatch” and “Blocking Statements!!” emphasize root causes.

---

## ✅ Best Practices Summary

| Area                         | Recommendation                                       |
|------------------------------|------------------------------------------------------|
| Sensitivity Lists            | Use `@(*)` for combinational logic                   |
| Sequential Logic             | Always use non-blocking assignments (`<=`)           |
| GLS Verification             | Run GLS with same testbench as RTL                   |
| Debugging                    | Compare waveforms side-by-side in GTKWave            |
| Code Structure               | Avoid mixing blocking/non-blocking in same block     |

---

## 🛠 Tools Used
- **IVERILOG** – Open-source Verilog simulator
- **GTKWave** – Waveform viewer for VCD files
- **Yosys** – Synthesis tool (implied from context)


---

## 📌 Final Takeaway

> **“If your simulation passes RTL but fails GLS, you have a bug — not a tool issue.”**

Always verify your design at the gate level. Mismatches are not just academic — they can lead to costly respins in silicon.


---

