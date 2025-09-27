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

</details> <details> <summary>Day 2 - Timing libs, Hierarchical vs Flat Synthesis and Efficient Flop Coding Styles</summary>
Content for Day 2 goes here.

</details> <details> <summary>Day 3 - Combinational and Sequential Optimizations</summary>
Content for Day 3 goes here.

</details> <details> <summary>Day 4 - GLS, Blocking vs Non-blocking and Synthesis-Simulation Mismatch</summary>
Content for Day 4 goes here.

</details> <details> <summary>Day 5 - Introduction to DFT</summary>
Content for Day 5 goes here.


</details
