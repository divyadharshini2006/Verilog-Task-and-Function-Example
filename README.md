# **EXP4: 4-bit Ripple Carry Adder using Task and 4-bit Ripple Counter using Function with Testbench**

---

## **Aim**
To design, simulate, and verify a **4-bit Ripple Carry Adder** using **Task** and a **4-bit Ripple Counter** using **Function** in Verilog HDL.

---

## **Apparatus Required**
- System with **Vivado Design Suite**
---

## **Theory**
### **Ripple Carry Adder**
A 4-bit Ripple Carry Adder (RCA) adds two 4-bit binary numbers by cascading four full adders. The carry-out of each full adder acts as the carry-in for the next stage. Using a **task** in Verilog, the addition operation can be modularized for each bit.
<img width="692" height="268" alt="image" src="https://github.com/user-attachments/assets/b70c348a-049a-4462-88a4-ad6905446cf4" />


### **Ripple Counter**
A Ripple Counter is a sequential circuit that counts in binary. In a 4-bit ripple counter, the output of one flip-flop serves as the clock input for the next flip-flop. Using a **function** in Verilog allows the counting logic to be encapsulated and reused.

<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/fa8730fc-e48e-477a-b2e3-6697a95355d3" />

---

## **Program**

### **4-bit Ripple Carry Adder using Task**

```verilog
// 4-bit Ripple Carry Adder using Task
module RippleCarryAdder_4bit (
    input [3:0] a,        
    input [3:0] b,        
    input cin,           
    output [3:0] sum,     
    output cout          
);
    reg [3:0] sum_reg;
    reg carry_out;
    assign sum = sum_reg;
    assign cout = carry_out;
    task full_adder;
        input a_bit, b_bit, carry_in;
        output sum_bit, carry_out_bit;
        begin
            sum_bit = a_bit ^ b_bit ^ carry_in;
            carry_out_bit = (a_bit & b_bit) | (carry_in & (a_bit ^ b_bit));
        end
    endtask
    reg c1, c2, c3;
    always @(*) begin
        full_adder(a[0], b[0], cin, sum_reg[0], c1);
        full_adder(a[1], b[1], c1, sum_reg[1], c2);
        full_adder(a[2], b[2], c2, sum_reg[2], c3);
        full_adder(a[3], b[3], c3, sum_reg[3], carry_out);
    end
endmodule
```

# Test Bench
```
module RippleCarryAdder_4bit_tb;
    reg [3:0] a, b;
    reg cin;
    wire [3:0] sum;
    wire cout;
    RippleCarryAdder_4bit uut (
        .a(a),
        .b(b),
        .cin(cin),
        .sum(sum),
        .cout(cout)
    );
    initial begin
        a = 4'b0001; b = 4'b0010; cin = 1'b0;
        #10;
        a = 4'b0101; b = 4'b0011; cin = 1'b1;
        #10;
        a = 4'b1111; b = 4'b1111; cin = 1'b1;
        #10;
        a = 4'b1010; b = 4'b0101; cin = 1'b0;
        #10;
        $stop;
    end
endmodule
```
### 4-bit Ripple Carry Adder Simulation Output 

<img width="1464" height="916" alt="image" src="https://github.com/user-attachments/assets/83974faa-22db-412f-854b-345c608f1b0e" />


### **4-bit Ripple Counter using Function**
```verilog
// 4-bit Ripple Counter using Function
module RippleCarryCounter_4bit (
    input clk,
    input reset,
    output [3:0] q
);
    reg [3:0] count;
    assign q = count;

    function [1:0] full_adder_func;
        input a_bit, b_bit, carry_in;
        begin
            full_adder_func[0] = a_bit ^ b_bit ^ carry_in;                   
            full_adder_func[1] = (a_bit & b_bit) | (carry_in & (a_bit ^ b_bit)); 
        end
    endfunction

    reg c1, c2, c3, c4; // Add one more carry variable

    always @(posedge clk or posedge reset) begin
        if (reset)
            count <= 4'b0000;
        else begin
            // Ripple increment by 1
            {c1, count[0]} = full_adder_func(count[0], 1'b1, 1'b0);
            {c2, count[1]} = full_adder_func(count[1], 1'b0, c1);
            {c3, count[2]} = full_adder_func(count[2], 1'b0, c2);
            {c4, count[3]} = full_adder_func(count[3], 1'b0, c3); 
        end
    end
endmodule
```

# Test Bench
```
module RippleCarryCounter_4bit_tb;
    reg clk, reset;
    wire [3:0] q;

    RippleCarryCounter_4bit uut (
        .clk(clk),
        .reset(reset),
        .q(q)
    );

    initial begin
        clk = 0;
        forever #5 clk = ~clk;
    end

    initial begin
        $monitor("Time=%0t | Reset=%b | Count=%b", $time, reset, q);
        reset = 1;
        #10 reset = 0;  
        #200 reset = 1;
        #10 reset = 0;
        #100 $stop;
    end
endmodule
```
### 4-bit Ripple Counter Simulation output 

<img width="1464" height="921" alt="image" src="https://github.com/user-attachments/assets/d8c1ffdd-70d4-4dd4-b729-c3691da76132" />


### Result

The simulation of the 4-bit Ripple Carry Adder using Task and 4-bit Ripple Counter using Function was successfully carried out in Vivado Design Suite.
Both designs produced correct functional outputs as verified by waveform and console output.
