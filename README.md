<details>
  <summary>Day 1 - Introduction to Verilog RTL Design and Synthesis</summary>

## Clone the repository to your local machine:
<img width="937" height="186" alt="Screenshot from 2025-09-27 15-39-01" src="https://github.com/user-attachments/assets/b3216360-d111-43a4-93ca-5fa4a28186fd" />

## Simulator Output - VCD File
output of simulator is a vcd fiie:

```bash
gtkwave tb_good_mux.vcd
```
<img width="1006" height="652" alt="Screenshot from 2025-09-27 16-00-15" src="https://github.com/user-attachments/assets/1139340a-d94d-4909-878f-70ebbf9bcb20" />

## Design and Testbench
The testbench applies stimulus to the design for functional verification.
<img width="1229" height="820" alt="Screenshot from 2025-09-27 16-18-46" src="https://github.com/user-attachments/assets/ec62cf35-6c7b-499b-84e6-64669f97b22e" />

## Liberty Files (.lib)

Liberty files contain characterization data for standard cell libraries:

- **Content:** Logical modules (AND, OR, NOT, etc.)
- **Variations:** Multiple drive strengths (slow, medium, fast)
- **Configurations:** Different input counts (2-input, 3-input, 4-input gates)
- **Purpose:** Provides timing, power, and area information for synthesis optimization

## Timing Considerations

### Setup Time Constraint
For proper sequential circuit operation:

```
T_clk > T_cq_A + T_combi + T_setup_B
```

Where:
- `T_clk`: Clock period
- `T_cq_A`: Clock-to-Q delay of source flip-flop
- `T_combi`: Combinational logic delay
- `T_setup_B`: Setup time of destination flip-flop

### Maximum Frequency
```
f_max = 1/T_clk
```

### Cell Selection Strategy

**Fast Cells:**
- Reduce combinational delays
- Help meet setup time requirements
- Higher power consumption and area

**Slow Cells:**
- Provide necessary delays for hold time requirements
- Prevent race conditions
- Lower power and area

## Synthesis Using Yosys

- Yosys synthesizer converts RTL to a gate-level netlist.
- It maps logic to standard cells from the provided `.lib`.
- Use the same testbench to verify the synthesized netlist functionality.
<img width="787" height="621" alt="Screenshot from 2025-09-27 17-11-06" src="https://github.com/user-attachments/assets/ef936cc5-644f-4220-bc43-a1179c39d75e" />
<img width="556" height="342" alt="Screenshot from 2025-09-27 17-11-56" src="https://github.com/user-attachments/assets/2e353de7-3790-4ba1-ac7f-30a03dc08724" />
<img width="525" height="184" alt="Screenshot from 2025-09-27 17-12-15" src="https://github.com/user-attachments/assets/9a364e8a-6350-416e-929d-94de42635565" />
<img width="629" height="672" alt="Screenshot from 2025-09-27 17-13-10" src="https://github.com/user-attachments/assets/8716b177-d8ff-463f-bdfe-6c2da520d61b" />

## Generating Netlist

Run Yosys with your RTL to produce the synthesized gate-level netlist:
<img width="759" height="347" alt="Screenshot from 2025-09-27 17-22-12" src="https://github.com/user-attachments/assets/f2f8a58f-dc13-4996-bbdb-86cb73c3053e" />
<img width="861" height="714" alt="Screenshot from 2025-09-27 17-25-07" src="https://github.com/user-attachments/assets/0c86af6e-dd81-411e-8729-159935a392b7" />

## Simplifying the Netlist

Optimize and simplify the netlist using Yosys:
<img width="791" height="362" alt="Screenshot from 2025-09-27 17-29-13" src="https://github.com/user-attachments/assets/6fdcf278-429d-4f3f-907c-d9f3c18acb0d" />
<img width="791" height="362" alt="Screenshot from 2025-09-27 17-29-13" src="https://github.com/user-attachments/assets/3deb47f5-f4ac-4fe0-b231-e087336cc790" />

## Workshop Tools Summary

- **Iverilog:** Simulation and verification
- **GTKWave:** Waveform visualization
- **Yosys:** Logic synthesis
- **Sky130 PDK:** Process design kit with standard cell libraries

</details> <details><summary>Day 2 - Timing Libraries, Hierarchical vs Flat Synthesis, and Efficient Flip-Flop Coding Styles</summary>

Overview of PVT in Liberty Files

The acronym PVT stands for Process, Voltage, and Temperature, which are key factors influencing chip behavior:
- Process: Manufacturing variations like doping and lithography affect chip characteristics.
- Voltage: Operating voltage variations impact speed and power usage.
- Temperature: Temperature changes influence performance and leakage.

A typical liberty file such as sky130_fd_sc_hd__tt_025C_1v80.lib indicates these parameters:
- tt = typical process corner
- 025C = 25°C temperature
- 1v80 = 1.80V voltage

These files provide detailed cell characterizations including power, timing, and drive strengths. Cells differ in size (e.g., and1, and2), where larger cells offer higher speed but consume more area and power.

Cell Variants and Area Tradeoffs

- Variants differ mainly in transistor sizes.
- Larger cells: faster but larger and more power hungry.
- Smaller cells: smaller area and less power, slower speed.

Hierarchical vs Flattened Synthesis

Stacked PMOS transistors increase resistance and slow down circuits; thus, NAND gates with parallel PMOS are preferred over NOR gates with stacked PMOS for better performance.

Yosys preserves hierarchy by default with write_verilog. To flatten modules into one monolithic module, run:

yosys> flatten

Synthesizing at a submodule level helps when multiple instances exist or to simplify large designs:

yosys> synth -top <submodule_name>

Flip-Flop Reset Methods

- Asynchronous reset: resets immediately when activated, ignoring clocks.
- Synchronous reset: resets at clock edges only, easing timing verification.

Mapping Flip-Flops in Yosys

Map flip-flops to library cells after synthesis by specifying the liberty file:

yosys> dfflibmap -liberty /path/to/sky130_fd_sc_hd__tt_025C_1v80.lib

Optimization Overview

Yosys optimizes gate count and performance, e.g., replacing multiply-by-2 with a shift. If no technology mapping is done, abc cannot generate a mapped netlist; show can display the current netlist.

Essential Yosys Commands:

read_verilog <file.v> : Read Verilog source code  
read_liberty -lib <file.lib>: Load timing library  
synth -top <module>: Synthesize top module  
flatten: Remove hierarchy  
dfflibmap -liberty <file>: Map flip-flops to cells  
abc -liberty <file.lib>: Technology mapping  
write_verilog <output.v>: Save synthesized netlist  
show: Display netlist  

</details><details><summary>Day 3 - Combinational and Sequential Optimization </summary>

Combinational Logic Optimization

- Constant propagation through circuit paths.
- Boolean simplification by Karnaugh maps or Quine–McCluskey.

Sequential Logic Optimization

Basic: Propagate constants through registers.  
Advanced:  
- State reduction by removing unreachable states.  
- Register cloning to reduce data path delay.  
- Retiming to improve maximum clock frequency.

Example: If D input of a flip-flop is always zero, output Q will remain zero—allowing the flip-flop removal. Async set/reset complicate such optimizations.

Lab Examples: Simplified AND expressions and state machines demonstrated graphically. Sequential optimization examples highlight when flip-flops can be optimized.

Optimization of Unused Outputs

Logic not dependent on all outputs can be trimmed. Synthesis report reflects whether flip-flops are removed accordingly.

</details><details><summary>Day 4 - Gate-Level Simulation (GLS), Blocking vs Non-Blocking Assignments, and Synthesis-Simulation Discrepancies</summary>

What is GLS

Gate-Level Simulation validates logical correctness and timing after synthesis by simulating the gate-level netlist.

Importance of GLS

- Ensures design behaves functionally like RTL.
- Confirms timing constraints are satisfied.

GLS with Iverilog

GLS requires gate models with timing in standard cell libraries.

Typical Netlist vs RTL Example

RTL: assign y = (a & b) | c;  

Gate-level:  
and a1(m, a, b);  
or o1(y, c, m);

Simulation triggers only when inputs change.

Why Functional Validation is Required

- Missing sensitivity lists (always @(sel) vs always @(*)) cause simulation errors.
- Difference between blocking (=) and non-blocking (<=) assignments affects execution order and simulation accuracy.

Blocking vs Non-Blocking Assignment Example

Blocking:  
q = q0;  
q0 = d;  

Non-blocking:  
q0 <= d;  
q <= q0;

Parallel updates simulate real flip-flop behavior more accurately.

Coding Caveats

Certain combinational constructs may produce correct outputs with different assignment types but GLS helps verify timing and correctness.

Labs

Multiplexer examples demonstrate proper GLS coding and highlight synthesis-simulation mismatches when coding styles are incorrect.

</details><details><summary>Day 5 - Synthesis Optimization</summary>

If-Else and Elif Ladders

These constructs implement prioritized conditional logic, synthesizing into cascaded multiplexers.

- The first true condition takes precedence.
- An else block provides a default when no conditions meet.

Example:

always @(*) begin  
  if (cond1)  
    y = a;  
  else if (cond2)  
    y = b;  
  else  
    y = e;  
end

Inferred Latch Risks and How to Avoid Them

Missing assignments in certain branches lead to inferred latches—usually undesirable.

Prevent Latches By:

- Including else or default assignments.
- Explicitly assigning to hold values when needed.

Case Statement Usage

- Switch-like selection for output assignments.
- Must cover all possible selectors with assignments or default to avoid latches.
- No priority; cases are mutually exclusive.

Example:

always @(*) begin  
  case(sel)  
    2'b00: y = a;  
    2'b01: y = b;  
    2'b10: y = c;  
    default: y = d;  
  endcase  
end

Verilog Loops

- For loops in always blocks: behavioral, unrolled at synthesis.
- Generate loops: instantiate multiple hardware modules outside behavioral blocks.

Example:

// Behavioral for loop  
always @(*) begin  
  for (i = 0; i < 8; i = i+1)  
    sum[i] = a[i] ^ b[i];  
end  

// Generate loop example  
genvar i;  
generate  
  for (i = 0; i < 4; i = i+1) begin : and_gen  
    and u_and(out[i], in1[i], in2[i]);  
  end  
endgenerate

Summary Table

Construct: if-else ladder — Recommendation: Always provide else/default case  
Construct: case statements — Recommendation: Assign all outputs; use default  
</details>
Construct: always for loop — Recommendation: Use for repeated assignments only  
Construct: generate for loop — Recommendation: Use for hardware instantiation outside always blocks

