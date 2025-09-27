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

</details> <details> <summary>Day 2 - Timing libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles</summary>

## PVT in Liberty Files

**PVT** refers to **Process, Voltage, and Temperature**, the three factors that impact the real-world behavior of silicon chips:

- **Process**: Slight differences introduced during fabrication (like doping or lithography) make each chip behave uniquely.
- **Voltage**: Chips may operate at different supply voltages, which directly influences delay and power consumption.
- **Temperature**: Higher temperatures increase leakage and reduce performance, while cooler chips behave faster.

A typical liberty filename such as `sky130_fd_sc_hd__tt_025C_1v80.lib` encodes these operating conditions:
- `tt` = typical process corner
- `025C` = 25°C
- `1v80` = 1.80V supply

This library contains detailed electrical and timing data for standard cells (e.g., AND, OR) under those conditions. It includes parameters like leakage, delay, output slew, and power usage. For a given logic function, there are multiple size variants, where larger transistors give higher speed but consume more area and power.

## Cell Variants and Area

- Different **variants** of the same cell (e.g., `and1`, `and2`) use different transistor sizing.
- **Bigger cells**: Provide faster operation but cost higher area and leakage.
- **Smaller cells**: Favor area and lower power but have slower performance.

## Hierarchical Synthesis

PMOS transistors connected in series (stacked) increase resistance, limiting performance. Synthesis prefers NAND gates (parallel PMOS) over NOR gates (stacked PMOS) for efficiency.

By default, Yosys keeps module hierarchy in the output netlist. Flattening the hierarchy is possible with:


```tcl
yosys> flatten
```

To synthesize at submodule level (for repeated instances or modular optimization):

```tcl
yosys> synth -top <sub_module_name>
```


## Flip-Flop Reset Approaches

- **Asynchronous reset**: Works immediately on reset state change, regardless of clock.
- **Synchronous reset**: Only activates reset on clock edges, making timing closure easier.

## Yosys Flow for Sequential Logic

After synthesizing, flip-flops must be mapped to real library cells with:
```tcl
yosys> dfflibmap -liberty /home/chippy/.volare/volare/sky130/versions/0fe599b2afb6708d281543108caf8310912f54af/sky130B/libs.ref/sky130_fd_sc_hd/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```


## Optimization Principles

- The tool reduces gate count and balances **area, power, and timing**.
- Multiplication by a constant (e.g., 2) is synthesized as a **shift operation**, not a multiplier.
- If `abc` is not used with a `.lib` file, the synthesized output won't map to physical standard cells, but `show` can still display the netlist.

## Common Yosys Commands

| Command | Purpose |
|---------|---------|
| `read_verilog <file.v>` | Load Verilog design |
| `read_liberty -lib <libfile.lib>` | Load technology library |
| `synth -top <module>` | Perform synthesis on top module |
| `flatten` | Remove hierarchy, make design single-module |
| `dfflibmap -liberty <libfile.lib>` | Bind flip-flops to library equivalents |
| `abc -liberty <libfile.lib>` | Map logic to gates from library |
| `write_verilog <out.v>` | Dump synthesized netlist |
| `show` | Visualize netlist |

</details> <details> <summary>Day 3 - Combinational and Sequential Optimizations</summary>

  ## Combinational Optimization

1. **Constant propagation**
2. **Algebraic simplification** using K‑maps or Quine–McCluskey approaches

---

## Sequential Logic Optimization

**Basic:**

* Constant propagation in sequential circuits

**Advanced:**

* State minimization
* Retiming to balance path delays

**Example:** A D flip-flop with `d=0` always outputs zero. Hence, the flip-flop can be removed as its value is fixed. But if there's an asynchronous set/reset tied, it cannot be optimized away since the async control alters Q independently. Optimizations apply only if the output is guaranteed constant.

**Advanced:**

* State minimization: eliminate unreachable states in FSMs.
* Register cloning: replicate registers near their loads to reduce routing delay.
* Retiming: shift registers across logic to reduce critical path delay and boost clock frequency.

---

## LAB

### Combinational

1. **opt_check.v** simplifies to `y = ab`.

   * `opt_clean -purge` optimizes away unused logic.
   * ![img1](https://github.com/user-attachments/assets/176b1a70-c7b2-4d6f-be47-ae456d24cee7)

2. **opt_check2.v** yields an AND–OR form.

   * ![img2](https://github.com/user-attachments/assets/53a81129-1ee2-4f7b-8c92-b6a5979022c3)

3. **opt_check3.v** expectation reduces to `y = abc`.

   * ![img3](https://github.com/user-attachments/assets/d744e502-e8cb-4836-bf2b-a98b101c650e)

4. Example 4

   * ![img4](https://github.com/user-attachments/assets/12263af4-bac6-4ead-b0b8-28c077a7a190)

5. **multiple_module_opt.v**

   * ![img5](https://github.com/user-attachments/assets/dcd680a4-810f-4d9a-91cb-c2cd575577d7)

6. Example 6

   * ![img6](https://github.com/user-attachments/assets/3ad075a5-f107-4cbc-a6c5-1f902b94b88c)

---

### Sequential

1. **Dff-const1** — `d=1`, `rst=1` keeps Q at 0, but when reset is released Q becomes 1 → no optimization possible.

   * ![img7](https://github.com/user-attachments/assets/64f65a86-eaa1-46ec-a74e-333cc1a69de1)
   * ![img8](https://github.com/user-attachments/assets/206a9eb8-2000-4b6b-ab75-f24e243b9647)

2. **Dff-const2** — `d=1`, with `set=1` → Q remains 1 permanently → optimizer removes FF.

   * ![img9](https://github.com/user-attachments/assets/9b7fafd0-6b59-436d-a344-171c8025f99e)
   * ![img10](https://github.com/user-attachments/assets/4d4b8334-09a2-4e5b-9fbb-8dd6a85fd8f2)

3. **Dff-const3** — Both FFs remain necessary, no optimization.

   * ![img11](https://github.com/user-attachments/assets/e032df7b-63a2-430f-9cb3-b74b7a17de66)

4. **Dff-const4** — Optimized away.

  * ![img12](https://github.com/user-attachments/assets/0bb5b8d9-c867-4d68-9781-4d4de9894f75)
  * ![img13](https://github.com/user-attachments/assets/da627113-e455-4d37-82a1-b8ce01a3e262)

5. **Dff-const5** — Q toggles, so register cannot be removed.

  * ![img14](https://github.com/user-attachments/assets/8988f37d-add7-4a8a-8171-7aa3d0dff0f6)

## Unused Output Optimization 

1. **3-bit UpCounter**
   - If only `count[0]` is used, the design reduces to a single toggling flip-flop, as higher bits are unnecessary.
   - If we check for `count==3'b100`, then all three flip-flops must remain.
   Thus, outputs that are not used are optimized away, reducing resources.

</details> <details> <summary>Day 4 - GLS, Blocking vs Non-blocking and Synthesis-Simulation Mismatch</summary>

  ## What is GLS
  GLS means **Gate Level Simulation**. RTL is tested functionally with a testbench. After synthesis, we test again with the netlist as DUT. Since the netlist is logically equivalent to RTL, we expect identical I/O behavior.

  ## Why GLS
  1. **Check design correctness after synthesis**
  2. **Ensure timing requirements are satisfied**

## GLS Using Iverilog
The synthesized netlist along with cell models is simulated. These models can include timing information (timing-aware) or just logic.

## What do we do in a GLS
Example:  
Design: assign y=(a&b)|c;  
Netlist: and a1(m,a,b);or o1(Y,c,m);
The simulator uses cells from library verilog models that can verify both **function and timing**.

Simulation event-driven nature: response only when inputs change.

## Why validate if RTL and netlist are logically same?
Because mismatches can occur due to:
- **Incomplete sensitivity list**
- **Blocking vs non-blocking misuse**
- **Improper Verilog constructs**

### Missing Sensitivity list:
Example: `always@(sel)` ignores input activity when `sel` doesn't toggle, creating latch-like behavior. The correct form is `always@(*)`.

### Blocking and Non Blocking
- `=` → Blocking, executes in order, like C statements.
- `<=` → Non-blocking, executes concurrently, proper for sequential logic.

In a shift register:
- **Blocking**: Statement order affects correctness, potentially shorting registers.
- **Non-blocking**: Ensures correct data propagation regardless of line order.

### Coding Caveat 
output reg q0;
always@*
y = q0 & c;
q0 = a|b;
Uses old q0 value before updating → mimics latch.
<hr>
output reg q0;
always@*
q0 = a|b;
y = q0 & c;
Assigns first then uses → no latch.

Both can yield same functional results in RTL sim, but GLS reveals real hardware behavior.

---

# LABS:
1) **MUX**
   <img width="1572" height="156" alt="image" src="https://github.com/user-attachments/assets/ec497281-598d-4592-9bfa-dae40f4a33f1" />
   <img width="608" height="218" alt="image" src="https://github.com/user-attachments/assets/310d7610-0ac6-48b5-a6a8-b53c764b113d" />

   GLS O/P
   <img width="1835" height="505" alt="image" src="https://github.com/user-attachments/assets/9ca05782-44a7-409e-92de-329298181beb" />

2) **BAD MUX**
 Activity on i0/i1 do not reflect in the output. RTL acts like a flop due to wrong sensitivity.  
   <img width="1560" height="305" alt="image" src="https://github.com/user-attachments/assets/776f9b86-6c09-47ee-97de-63a916f908b9" />  
   GLS simulation maps correctly to hardware, so mismatch arises:  
    <img width="1840" height="563" alt="image" src="https://github.com/user-attachments/assets/339c870f-fa43-4857-897c-c20cc6b076aa" />

</details> 

<details> <summary>Day 5 - Optimization In synthesis</summary>

  ## If-Else and Elif Ladder in Verilog
`if-else` and `else if` in Verilog create *priority logic*. The first condition that evaluates true is selected, ignoring the rest. If none are true, the `else` path executes.

**Example:**
```verilog
always @(*) begin
  if (cond1)
    y = a;
  else if (cond2)
    y = b;
  else
    y = E;
end
```

**Hardware View:**
This corresponds to chained multiplexers, where the first satisfied condition chooses the output. The `else` branch acts as a fallback.

***

## Inferred Latches: Problems and Avoidance

When a combinational always block does not cover all possible assignments for a signal, synthesis infers a **latch** to hold the last value.

**Example:**
```verilog
always @(*) begin
if (cond1)
y = a;
else if (cond2)
y = b;
// missing else means latch
end
```

**Why risky?**
- Inferred latches cause unpredictable behavior and timing complexity, often unintended.

**Best Approach:**
- Always provide an `else` or `default`.  
- If you need to retain value, explicitly assign `y = y;`.

***

## Case Statements: Behavior and Considerations

Case statements handle multi-way choices, effectively implementing multiplexers.

**Example:**
```verilog
always @(*) begin
case (sel)
2'b00: y = a;
2'b01: y = b;
2'b10: y = c;
default: y = d;
endcase
end
```

**Important Notes:**
- Target signal must be type `reg`.
- Missing `default` may infer latches if not all values covered.
- Each branch must assign outputs fully.
- `case` has **no priority**; conditions are parallel but exclusive.
- Overlapping case items are illegal.

**If-Else vs Case:**
- Use `if-else` when priority matters.
- Use `case` for mutually exclusive selection.

***

## Loops in Verilog

Two main loop constructs:

### Inside always: For Loops
- Repeat behavior in combinational logic.
- Synthesized as unrolled hardware copies, not runtime loops.
- Handy for wide structures.

**Example:**
```verilog
always @(*) begin
for (i = 0; i < 8; i = i + 1)
sum[i] = a[i] ^ b[i];
end
```

### Generate Loops
- Instantiate multiple hardware blocks.
- Defined outside always blocks.
- Common for generic structures (buses, FIFOs).

**Example:**
```verilog
genvar i;
generate
for (i = 0; i < 4; i = i + 1) begin : and_gen
and u_and (out[i], in1[i], in2[i]);
end
endgenerate
```

***

## Summary Table: Recommended Practices

| Construct         | Suggested Practice                               |
|------------------|-------------------------------------------------|
| if-else ladder   | Always include an else to avoid latches         |
| case statement   | Handle all values; assign every output branch   |
| always for loop  | For replicated combinational assignments only   |
| generate for     | For module/logic instantiations only            |

***
</details>
