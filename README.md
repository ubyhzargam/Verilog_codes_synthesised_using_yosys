# Verilog_codes_synthesised_using_yosys
These are verilog codes that are synthesized using open source tool yosys


1. FULL ADDER (1 BIT) :
   
   
a. Full adder code -
   
module fa(a,b, cin, sum, co) ;
input a,b, cin;
output sum, co;
assign sum=a^b^cin;
assign co=(a&b) | (a&c) | (b&c) ;
endmodule

b. Generic logic gate netlist using yosys - 

<img width="1470" alt="Screenshot 2024-11-10 at 1 47 14 AM" src="https://github.com/user-attachments/assets/731341ac-6bba-4c05-b617-81865203f821">


  
