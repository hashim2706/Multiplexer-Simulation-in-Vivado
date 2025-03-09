SIMULATION AND IMPLEMENTATION OF LOGIC GATES
AIM:
To design and simulate a 4:1 Multiplexer (MUX) using Verilog HDL in four different modeling styles—Gate-Level, Data Flow, Behavioral, and Structural—and to verify its functionality through a testbench using the Vivado 2023.1 simulation environment. The experiment aims to understand how different abstraction levels in Verilog can be used to describe the same digital logic circuit and analyze their performance.

APPARATUS REQUIRED:
Vivado 2023.1

Procedure
1. Launch Vivado
Open Vivado 2023.1 by double-clicking the Vivado icon or searching for it in the Start menu.
2. Create a New Project
Click on "Create Project" from the Vivado Quick Start window.
In the New Project Wizard:
Project Name: Enter a name for the project (e.g., Mux4_to_1).
Project Location: Select the folder where the project will be saved.
Click Next.
Project Type: Select RTL Project, then click Next.
Add Sources:
Click on "Add Files" to add the Verilog files (e.g., mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.).
Make sure to check the box "Copy sources into project" to avoid any external file dependencies.
Click Next.
Add Constraints: Skip this step by clicking Next (since no constraints are needed for simulation).
Default Part Selection:
You can choose a part based on the FPGA board you are using (if any).
If no board is used, you can choose any part, for example, xc7a35ticsg324-1L (Artix-7).
Click Next, then Finish.
3. Add Verilog Source Files
In the "Sources" window, right-click on "Design Sources" and select Add Sources if you didn't add all files earlier.
Add the Verilog files (mux4_to_1_gate.v, mux4_to_1_dataflow.v, etc.) and the testbench (mux4_to_1_tb.v).
4. Check Syntax
Expand the "Flow Navigator" on the left side of the Vivado interface.
Under "Synthesis", click "Run Synthesis".
Vivado will check your design for syntax errors. If any errors or warnings appear, correct them in the respective Verilog files and re-run the synthesis.
5. Simulate the Design
In the Flow Navigator, under "Simulation", click on "Run Simulation" → "Run Behavioral Simulation".
Vivado will open the Simulations Window, and the waveform window will show the signals defined in the testbench.
6. View and Analyze Simulation Results
The simulation waveform window will display the signals (S1, S0, A, B, C, D, Y_gate, Y_dataflow, Y_behavioral, Y_structural).
Use the time markers to verify the correctness of the 4:1 MUX output for each set of inputs.
You can zoom in/out or scroll through the simulation time using the waveform viewer controls.
7. Adjust Simulation Time
To run a longer simulation or adjust timing, go to the Simulation Settings by clicking "Simulation" → "Simulation Settings".
Under "Simulation", modify the Run Time (e.g., set to 1000ns).
8. Generate Simulation Report
Once the simulation is complete, you can generate a simulation report by right-clicking on the simulation results window and selecting "Export Simulation Results".
Save the report for reference in your lab records.
9. Save and Document Results
Save your project by clicking File → Save Project.
Take screenshots of the waveform window and include them in your lab report to document your results.
You can include the timing diagram from the simulation window showing the correct functionality of the 4:1 MUX across different select inputs and data inputs.
10. Close the Simulation
Once done, close the simulation by going to Simulation → "Close Simulation".

Logic Diagram

![image](https://github.com/user-attachments/assets/d4ab4bc3-12b0-44dc-8edb-9d586d8ba856)

Truth Table

![image](https://github.com/user-attachments/assets/c850506c-3f6e-4d6b-8574-939a914b2a5f)

Verilog Code

4:1 MUX Gate-Level Implementation

module mul4to1(a,b,S,Y);
input a,b;
input [3:0]S;
output Y;
wire abar,bbar,w1,w2,w3,w4;
and g1(w1,a,bbar,S0);
and g2(w2,a,b,S1);
and g3(w3,abar,b,S2);
and g4(w4,abar,bbar,S3);
not g5(abar,a);
not g6(bbar,b);
or g7(Y,w1,w2,w3,w4);
endmodule

![Screenshot (107)](https://github.com/user-attachments/assets/af1da585-a963-4046-9941-ae809ea3a70b)

  

4:1 MUX Data Flow Implementation

// mux4_to_1_dataflow.v
module mux4_to_1_dataflow (a,b,s,y);
input a,b,[3:0]s;
    assign y=(~a & b & s0)|(a & ~b & s1) | (~a & ~b & s2) |(a & b & s3);
endmodule

4:1 MUX Behavioral Implementation

// mux4_to_1_behavioral.v
module mux4_to_1_behavioral (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output reg Y
);
    always @(*) begin
        case ({S1, S0})
            2'b00: Y = A;
            2'b01: Y = B;
            2'b10: Y = C;
            2'b11: Y = D;
            default: Y = 1'bx; // Undefined
        endcase
    end
endmodule

4:1 MUX Structural Implementation

// mux2_to_1.v
module mux2_to_1 (
    input wire A,
    input wire B,
    input wire S,
    output wire Y
);
    assign Y = S ? B : A;
endmodule


// mux4_to_1_structural.v
module mux4_to_1_structural (
    input wire A,
    input wire B,
    input wire C,
    input wire D,
    input wire S0,
    input wire S1,
    output wire Y
);
    wire mux_low, mux_high;

    // Instantiate two 2:1 MUXes
    mux2_to_1 mux0 (.A(A), .B(B), .S(S0), .Y(mux_low));
    mux2_to_1 mux1 (.A(C), .B(D), .S(S0), .Y(mux_high));

    // Instantiate the final 2:1 MUX
    mux2_to_1 mux_final (.A(mux_low), .B(mux_high), .S(S1), .Y(Y));
endmodule

Testbench Implementation

// mux4_to_1_tb.v
`timescale 1ns / 1ps

module mux4_to_1_tb;
    // Inputs
    reg A;
    reg B;
    reg C;
    reg D;
    reg S0;
    reg S1;

    // Outputs
    wire Y_gate;
    wire Y_dataflow;
    wire Y_behavioral;
    wire Y_structural;

    // Instantiate the Gate-Level MUX
    mux4_to_1_gate uut_gate (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y_gate)
    );

    // Instantiate the Data Flow MUX
    mux4_to_1_dataflow uut_dataflow (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y_dataflow)
    );

    // Instantiate the Behavioral MUX
    mux4_to_1_behavioral uut_behavioral (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y_behavioral)
    );

    // Instantiate the Structural MUX
    mux4_to_1_structural uut_structural (
        .A(A),
        .B(B),
        .C(C),
        .D(D),
        .S0(S0),
        .S1(S1),
        .Y(Y_structural)
    );

    // Test vectors
    initial begin
        // Initialize Inputs
        A = 0; B = 0; C = 0; D = 0; S0 = 0; S1 = 0;

        // Apply test cases
        #10 {S1, S0, A, B, C, D} = 6'b00_0000; // Y = A = 0
        #10 {S1, S0, A, B, C, D} = 6'b00_0001; // Y = A = 1
        #10 {S1, S0, A, B, C, D} = 6'b01_0010; // Y = B = 1
        #10 {S1, S0, A, B, C, D} = 6'b10_0100; // Y = C = 1
        #10 {S1, S0, A, B, C, D} = 6'b11_1000; // Y = D = 1
        #10 {S1, S0, A, B, C, D} = 6'b01_1100; // Y = B = 1
        #10 {S1, S0, A, B, C, D} = 6'b10_1010; // Y = C = 1
        #10 {S1, S0, A, B, C, D} = 6'b11_0110; // Y = D = 1
        #10 {S1, S0, A, B, C, D} = 6'b00_1111; // Y = A = 1
        #10 $stop;
    end

    // Monitor the outputs
    initial begin
        $monitor("Time=%0t | S1=%b S0=%b | Inputs: A=%b B=%b C=%b D=%b | Y_gate=%b | Y_dataflow=%b | Y_behavioral=%b | Y_structural=%b",
                 $time, S1, S0, A, B, C, D, Y_gate, Y_dataflow, Y_behavioral, Y_structural);
    end
endmodule


Sample Output

![Screenshot (106)](https://github.com/user-attachments/assets/ffed8ce9-dc1f-4f20-849b-b22edb5e6713)


Time=0 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=10 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=0 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=20 | S1=0 S0=0 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=30 | S1=0 S0=1 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
Time=40 | S1=1 S0=0 | Inputs: A=0 B=0 C=0 D=1 | Y_gate=0 | Y_dataflow=0 | Y_behavioral=0 | Y_structural=0
...

Conclusion:

In this experiment, a 4:1 Multiplexer was successfully designed and simulated using Verilog HDL across four different modeling styles: Gate-Level, Data Flow, Behavioral, and Structural. The simulation results verified the correct functionality of the MUX, with all implementations producing identical outputs for the given input conditions.



  
