# Verilog_codes_synthesised_using_yosys
These are verilog codes that are synthesized using open source tool yosys<br>
Understanding what is synthesis is important. Anyone can produce the synthesized netlist once we give codes. Understanding the entire process of Logic syntheis helps us make less errors, synthesize effectively and also read results in a better manner. 
Logic synthesis consists of various steps - The first one begin RTL synthesis, then logic optimization, then technology mapping and then finally technology mapped optimization. <br>
RTL synthesis is the first step of logic synthesis. In this step the verilog code is converted to netlist consisting of generic logic gates. This is further divided into parsing, elaboration and then finally optimization+translation. This step is important as it tells us how the synthesis tool converts the verilog code to syntax tree (known as parsing) and then this is converted to internal model representation. We should also be aware of what is sythesizable and what is not. <br>
After RTL synthesis, we have logic optimization on generic netlist generated by previous step. This is of two types - 2 level (usually done using Quine's algorithm and aims to find minimal cover) and multilevel logic optimization ( Having idea about directed acyclic graph(boolean logic network) and factored form and various multilevel logic optimization techniques such as eliminate, simplify, extract and substitute help in understanding how the tool optimizes).<br>
Then we have technology mapping where we map the netllist to a standard cell present in the library using proess design kit(PDK). The information of area, delay, power and many such information is present in the library in the form of hierarchy. We should know why the creation of libaries is done and what is the information present in libraries and what are the various modelling schemes used in libaries to model delays, power , etc (NLDM, ECS, CCS, lumped modelling of RC interconnects, distributed modelling of RC and so on). So, in this step we perform technology mapping . <br>
In the final step we do optimization on technology mapped netlist <br>
Having idea about hypercubes, SAT solvers, ROBDD (reduced ordered binary decision diagrams) and much more concepts related to how the tool reads verilog language, uses information from libary, generates netlist and does logic optimization. It also explains how the circuits can be automated to such a great level of abstraction using data structures which model behavior of circuits. 

# 1. FULL ADDER (1 BIT) :
   
   
a. Full adder code -
   
module top(a,b, cin, sum, co) ;<br>
input a,b, cin;<br>
output sum, co;<br>
assign sum=a^b^cin;<br>
assign co=(a&b) | (a&c) | (b&c) ;<br>
endmodule<br>
<br>
<br>


b. Internal model representation - 
This is known as syntax tree. This is the first stage of RTL synthesis known as parsing. 

<img width="1470" alt="Screenshot 2024-11-10 at 1 47 14 AM" src="https://github.com/user-attachments/assets/731341ac-6bba-4c05-b617-81865203f821">
<br>
<br>

c. Technlogy mapping - 

I used a 45nm pdk and typical process to do technology mapping. 
I created a yosys script instead of giving individual commands. The script is -<br>
 #read design<br>
read_verilog top.v<br>
#elaborate design hierarchy<br>
hierarchy -check -top top<br>
#mapping to internal cell library<br>
synth -top top<br>
#mapping flip-flops to mycells.lib<br>
#dfflibmap -liberty NangateOpenCellLibrary_typical.lib<br>
#mapping logic to mycells.lib<br>
abc -liberty NangateOpenCellLibrary_typical.lib<br>
#cleanup<br>
clean<br>
#write synthesized design<br>
write_verilog -noattr synth.v<br>

I got the following netlist - 
<img width="1462" alt="Screenshot 2024-11-21 at 10 12 22 PM" src="https://github.com/user-attachments/assets/96b12e00-e9a2-4a05-b614-4386d08f4f55">


# 2. 4:1 demux

a. Code - <br>

module demux(out0,out1,out2,out3,s1,s0,in); <br>
output out0,out1,out2,out3;<br>
reg out0,out1,out2,out3;<br>
input s1,s0,in;<br>
always @(in or s1 or s0)<br>
case({s1,s0})<br>
2'b00: begin out0=in;out1=1'bz;out2=1'bz;out3=1'bz; end<br>
2'b01: begin out0=1'bz;out1=in;out2=1'bz;out3=1'bz; end<br>
2'b10: begin out0=1'bz;out1=1'bz;out2=in;out3=1'bz; end<br>
2'b11: begin out0=1'bz;out1=1'bz;out2=1'bz;out3=in; end<br>
2'b1x,2'b0x,2'bx1,2'bx0,2'bxx,2'bzx,2'bxz : begin<br>
out0=1'bx;out1=1'bx;out2=1'bx;out3=1'bx; end<br>
2'b1z,2'b0z,2'bz1,2'bz0,2'bzz : begin <br>
out0=1'bz;out1=1'bz;out2=1'bz;out3=1'bz; end<br>
default :$display("Invalid select signal");<br>
endcase<br>
endmodule<br>

<br>
<br>

b. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-10 at 11 57 48 PM" src="https://github.com/user-attachments/assets/1ea80327-96e4-4927-baba-280c1c09dd8a">

<br>
<br>

c. Technlogy mapping - 

I used a 45nm pdk and typical process to do technology mapping. 
I created a tcl script instead of giving individual commands. The script is -<br>

#read modules from Verilog file<br>
 read_verilog top.v<br>

hierarchy -check -top top<br>

#translate processes to netlists<br>
proc<br>

#mapping to internal cell library<br>
 techmap<br>

#mapping flip-flops to NangateOpenCellLibrary_typical.lib<br>
#for eg. always block<br>

dfflibmap -liberty NangateOpenCellLibrary_typical.lib<br>

#mapping logic to NangateOpenCellLibrary_typical.lib<br>
#for eg. assign block<br>
abc -liberty NangateOpenCellLibrary_typical.lib<br>

 #remove unused cells and wires<br>
clean<br>
#Write the current design to a Verilog file<br>
write_verilog -noattr synth_example.v<br>

<img width="1462" alt="Screenshot 2024-11-21 at 10 19 35 PM" src="https://github.com/user-attachments/assets/af6ab3e5-146b-4d97-bdb7-f07b972dfe25">


<br>
<br>

d. Chip area calculation -

Using the command : stat -liberty NangateOpenCellLibrary_typical.lib

<img width="1470" alt="Screenshot 2024-11-11 at 12 08 26 AM" src="https://github.com/user-attachments/assets/b8f6749e-a786-4904-8e58-0c81355ed376">

<br>
<br>

# 3. FULL ADDER (4 BIT) using structural abstraction :

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 23 16 AM" src="https://github.com/user-attachments/assets/18489a2b-d32e-4685-9d19-f5c98d74ba7d">

<br>
<br>

# 4. FULL ADDER (4 BIT) using dataflow :

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 36 56 AM" src="https://github.com/user-attachments/assets/55c9a973-6845-4b5b-8cec-e18fca41b33b">

<br>
<br>

b. Technlogy mapping - 
<img width="1455" alt="Screenshot 2024-11-21 at 10 23 32 PM" src="https://github.com/user-attachments/assets/93afcc2f-45fb-47c6-bf80-99142e4df18f">


<br>
<br>

c. Area - 
<img width="559" alt="Screenshot 2024-11-21 at 10 25 18 PM" src="https://github.com/user-attachments/assets/93fa8f8a-7e47-473f-8bfc-d85bbef7c85b">

<br>
<br>

# 5. Edge detector :

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 43 47 AM" src="https://github.com/user-attachments/assets/5895f55d-cce2-4256-9618-82a9ad2ba610">

<br>
<br>

b. Technology mapping - 

<img width="769" alt="Screenshot 2024-11-21 at 10 26 43 PM" src="https://github.com/user-attachments/assets/d638ceea-d648-440c-8551-ec6d0b697b30">


<br>
<br>

c. Area - 

<img width="523" alt="Screenshot 2024-11-21 at 10 27 22 PM" src="https://github.com/user-attachments/assets/8a9bbc7f-6084-4019-8c44-dcf6db5a61e4">


<br>
<br>

# 6. BCD counter :

a. Internal model representation - 

<img width="1470" alt="Screenshot 2024-11-11 at 12 54 23 AM" src="https://github.com/user-attachments/assets/e7001db8-bc9c-4c30-bdbb-c47fc49965f7">

<br>
<br?

b. Technology mapping - 

<img width="1464" alt="Screenshot 2024-11-21 at 10 30 35 PM" src="https://github.com/user-attachments/assets/de42d1b8-9eac-4c93-a72e-f263787fd237">


<br>
<br>

c. Area - 
<img width="493" alt="Screenshot 2024-11-21 at 10 30 54 PM" src="https://github.com/user-attachments/assets/7c2bb661-5ce4-49c2-a0c3-cc2ebcecb18f">


<br>
<br>

# 7. Carry look ahead adder (4 bit) :

a. Internal model representation -

<img width="1470" alt="Screenshot 2024-11-11 at 1 00 15 AM" src="https://github.com/user-attachments/assets/5167be3c-912b-4a95-bc35-0afae0f4bab0">

<br>
<br>

b. Technology mapping - 

<img width="1079" alt="Screenshot 2024-11-21 at 10 33 02 PM" src="https://github.com/user-attachments/assets/0a541863-c19c-44b8-89c2-5c41c910f8b7">

<br>
<br>

c. Area - 

<img width="1470" alt="Screenshot 2024-11-11 at 1 02 19 AM" src="https://github.com/user-attachments/assets/cd73355c-a13f-4d37-904e-34cd1ec71e5f">

<br>
<br>

# 8 Overflow detector :

a. Generic logic gate -<br>
I used different set of commands this time. Instead of generating a internal model, I generated a generic logic gate netlist using the following command - 
read_verilog top.v; <br>synth -top top;<br> write_verilog top_netlist.v<br>

<img width="1405" alt="Screenshot 2024-11-19 at 1 06 44 AM" src="https://github.com/user-attachments/assets/0cbb2349-f7b1-4751-9ae1-4e84c5a8f895">

<br>
<br>

b. Generic gate logic optimization - <br>
read_verilog mydesign.v;<br> synth; <br> opt; <br> clean; <br> write_verilog mydesign_optimized.v<br> 
<img width="1470" alt="Screenshot 2024-11-19 at 1 22 45 AM" src="https://github.com/user-attachments/assets/b9f24319-cde3-4d27-be94-7617903a24d6">


c. Technology mapping - <br>
I also updated the technology mapping commands to get a better view of the gates and mapping - <br>
read_liberty -lib NangateOpenCellLibrary_typical.lib<br>
read_verilog top.v<br>
proc techmap<br>
dfflibmap -liberty NangateOpenCellLibrary_typical.lib<br>
abc -liberty NangateOpenCellLibrary_typical.lib<br>
write_verilog -noattr top_mapped.v<br>

<img width="1463" alt="Screenshot 2024-11-21 at 10 34 35 PM" src="https://github.com/user-attachments/assets/22fc85d9-13ce-46e9-ba7e-71a4dfa2b032">
br>

<br>
<br>

d. Area - <br>
<img width="428" alt="Screenshot 2024-11-21 at 10 35 25 PM" src="https://github.com/user-attachments/assets/d38e1fa4-8fbb-4c18-89a9-e65d71c50349">


<br>
<br>

d. Logic Optimization - <br>
Commands opt was given<br>
Also resource sharing was made possible using the command share -aggressive<br>
<img width="517" alt="Screenshot 2024-11-19 at 12 59 26 AM" src="https://github.com/user-attachments/assets/7b4d2fbe-9660-466c-88e7-f08abf28544f"><br>
<img width="403" alt="Screenshot 2024-11-19 at 12 59 53 AM" src="https://github.com/user-attachments/assets/c5991bfe-70c4-4090-9800-c8a6f37a2a40"><br>

SAT word used above stands for Satisfiability solvers. SAT solvers and ROBDD are used extensively for represetation of Boolean functions. They are canonical and compact and make optimization and verification of circuits easier. <br>

<img width="45" alt="Screenshot 2024-11-19 at 12 58 04 AM" src="https://github.com/user-attachments/assets/043013c3-c8df-49c3-a9e3-11ad331ff8a4">

# 9 FSM to detect more than one 1s in the last 3 samples :

a. Internal model representation - <br>
<img width="492" alt="Screenshot 2024-11-21 at 9 21 12 PM" src="https://github.com/user-attachments/assets/6d7dd888-da26-4e37-8d08-db01b92c01e1">

<br>
<br>
b. Generic logic gate -<br>

<img width="1470" alt="Screenshot 2024-11-21 at 9 22 56 PM" src="https://github.com/user-attachments/assets/f69167f7-08a3-4f0b-a330-73b0c9c2ddcb">
<br>
<br>

c. Optimized Generic logic gate -<br>
<img width="1470" alt="Screenshot 2024-11-21 at 9 25 01 PM" src="https://github.com/user-attachments/assets/3443c5bb-d239-45ae-82ef-3d2798c089af">

<br>
<br>

d. Technology mapped optimised netlist - <br>
<img width="1470" alt="Screenshot 2024-11-21 at 9 46 27 PM" src="https://github.com/user-attachments/assets/8a18945b-6584-47c1-97c6-638385e6309e">

<br>
<br>

e. Area - <br>
<img width="462" alt="Screenshot 2024-11-21 at 9 49 42 PM" src="https://github.com/user-attachments/assets/dd2f1b8f-464d-43dd-8239-39facaef69cb">

<br><br>  
# This is the end of repository, the learning about yosys is complete. After this point it is very easy to go look up their website and read what individual command does and then use it. With this knowledge, it should be easy to synthesize any circuit easily.
<br><br>
