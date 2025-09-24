## Week 1: Introduction to Icarus Verilog & GTKWave 
This is the follow up of my earlier repository about my journey in VSD workshop. This week i learnt the methods of using the different EDA tools that I downloaded the preceding week. 🎉  
## DAY 1:
We got hands-on with two essential open-source tools for Verilog simulation and waveform visualization:

- **Icarus Verilog (`iverilog`)** – A Verilog simulation and synthesis tool.
- **GTKWave** – A powerful waveform viewer for analyzing simulation results.

---

## What We Covered

✅ **Compiling Verilog code using `iverilog`**  
✅ **Running simulations and generating waveform dumps (`.vcd` files)**  
✅ **Viewing and analyzing waveforms in GTKWave**

---

## Quick Start Example

1. **Compile** your Verilog file:
   ```bash
   iverilog your_design.v testbench.v
   ```

2. **Run** the simulation:
   ```bash
   ./a.out
   ```

3. **Open** the waveform in GTKWave:
   ```bash
   gtkwave testbench.vcd
   ```

---

## Screenshots

Below are snapshots from our Day 1 session:

![iverilog compilation](screenshots/iverilog_compile.png)  
*Compiling Verilog code with Icarus Verilog*

![gtkwave waveform](screenshots/gtkwave_view.png)  
*Viewing simulation results in GTKWave*

---

