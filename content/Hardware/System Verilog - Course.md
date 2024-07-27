---
created: 2024-04-26 12:29
updated: 2024-07-27T18:58
modified: 2024-07-27T18:57:55+02:00
tags:
  - ready
---
# Lectures
## 1. Intro
![[Pasted image 20240426142115.png|500]]
```Verilog
module mux(input wire a, b, s, output wire o);
	wire ns;
	wire sa;
	wire nsb;

	assign ns = !s;
	assign sa = a & s;
	assign nsb = b & ns;
	assign o = sa | nsb';
endmodule

module mux (input wire a, b, s output wire reg o);
	always @(a or b or s) begin
		if (s==1'b1) begin
            o = a;
		end else begin
            o = b;
		end
	end
endmodule
```

## 1a. Hello World
`inital` - pure simulation construct
`always` - may be synthesizable
```Verilog
module test();
    // initial block evaluates only once (at time 0)

    // both run in parallel - racking condition
    initial $display("Hello World");
    initial $display("Hello Again");

    // now evaluated sequentialy
    initial begin
        initial $display("Hello World");
        initial $display("Hello Again");
    end

    // add delay
    initial begin
        initial $display("Hello World");
        #1ns;
        initial $display("Hello Again");
    end

    // always block evaluates repeatedly, and requires trigger event
    always #1ns begin
        $display("Hello World");  // will be printed every 1ns
    end

    always @(posedge clk) begin //negedge
        $display("Hello World");
    end
endmodule
```
## 2. Signal Modeling
`wire` - can be use with **continuous** assignment 
`reg` - should only be use with **sequential** statement, or with **procedural** statement like the `inital` block 
`logic` - more versatile, can be use with all: **procedural**, **sequential** statements and **continuous** assignments 
*=* - blocking assignment
*<=* - nonblocking assignment
```verilog
module dut(input wire clk);
    wire w0, w1;
    reg r0, r1;
	// or instead use logic
    logic l0, l1, l2
    
    wire[7:0] wx8;
    reg[7:0] rx8; 

    // continuous assignment
    assign w0 = 1'b1;
    assign w1 = r0 & w0;

    // sequential statement
    always @(posedge clk or posedge reset) begin
        r1 = r0 & w0;
    end

    initial r0 = 1'b1;

	// ! wire can be connected to multiple drivers
	// but this is illegal for `logic` type
	assign w0 = w1;
	assign w0 = w2;
	
endmodule
```
## 3. Numerical Variables
![[Pasted image 20240426144942.png|500]]
![[Pasted image 20240426145527.png|500]]
```verilog
module test();
    byte byte0;     //8'b00000000
    logic[7:0] l8;  //8'bxxxxxxxx

    initial begin
        byte0 = l8;
        if(byte0 == l8) $display("byte0==l8");      //exec
        if(byte0 === l8) $display("byte0====l8");   //no exec
        if($isunknown(l8)) $display("$isunknown");  //exec
    end 
endmodule
```

## 4. Enumeration
```verilog
module test();
    parameter RED=1, GREEN=2;
    parameter DAY=0, NIGHT=1;
    int colour;
    int mode;

    // or event better
    enum int {RED=1, GREEN=2} colour;
    enum int {DAY, NIGHT} mode

    initial begin
        colour = RED;
        $display("colour=%0d", colour);
        mode = DAY
        $display("mode=%0d", mode);
    
        // in a case of enum
        colour = 0 // ! is not allowed 
        $cast(colour, 0)

        colour = colour.first(); //RED
        colour = colour.next(); //GREEN
        colour = colour.last();
        $display("colour=%0d, %0s", colour, colour.names())
    end;
    
endmodule
```
## 5. String
```Verilog
module test();
    string sHello = "Hello World";
    logic[7:0] i = 4'd12;
    $display("i=%d", i);
    $display("i=%0d", i); // 0 - to get rid of displaying spaces
    $display("i=%0d, %0b, %0h", i, i, i);

    // concatenatino
    string sHello2 = {sHello, namesHello};
    string str = "1234"
    i = str.len(); // 4
    i = str.atoi(); // 1234
    str.itoa(i) // i.e. from number to string
endmodule
```
## 6. Structures
```Verilog
// Create structure
module test ();
	struct {bit b; byte byt; int i;} s0, s1;

	initial begin()
		s0.b = 1'b0;
		s0.byt = 8'd7;
		s0.i = 32'habcd;
		s1 = s0;
	end
endmodule

// Name your structure
module test ();
	typedef struct {bit b; byte byt; int i;} myStruct;
	myStruct s0, s1;

	initial begin()
		s0.b = 1'b0;
		s0.byt = 8'd7;
		s0.i = 32'habcd;
		s1 = s0;
	end
endmodule

// So instead of writing
module test();
	bit[7:0] red0, red1, red2;
	bit[7:0] green0, green1, green2;
	bit[7:0] blue0, blue1, blue2;
	initial begin
		red0 = 8'h0;
		green0 = 8'h0;
		blue0 = 8'hff;
		red1 = red0; green1 = green0; blue1 = blue0;
		red2 = red0; green2 = green0; blue2 = blue0;
	end
endmodule

// So it is better to rewrite as 
module test();
	struct {
		bit[7:0] red, green, blue;
	} pixel0, piexl1, piexl2;
	initial begin
		pixel0.red = 8'h0;
		pixel0.green0 = 8'h0;
		pixel0.blue0 = 8'hff;
		pixel1 = pixel0;
		pixel2 = pixel0;
	end
endmodule
```
Additional syntax
![[Pasted image 20240426124148.png|500]]

```Verilog
struct {bit b; int i;} s0;
packed struct {bit b; int i;} s0;
```
### Summary
- `struct` is an aggregate data type which can contains multiple variables of different types 

## 7. Fixed Size Array
```Verilog
module test();
    bit b[1:0];
    initial begin
        b[0] = 1'b0;
        b[1] = 1'b1;
        // or
        b = '{1'b1, 1'b0};
        // or
        for (int i=0; i<2; i++) begin // better to use $size(b) instead of hardcodding 2
            b[i] = i;
        end
        // or
        foreach (b[i]) begin
            b[i] = i;
        end
    end
endmodule
```
![[Pasted image 20240426151813.png|500]]


## 8. Variable Size Array
![[Pasted image 20240427204544.png|500]]
```Verilog
byte data[3:0];
byte numData = 3;
for (int i=0; i<numData; i++) begin
	dataOut = data[i];
	#10ns;
end

module test();
	byte b[99:0];
	//used only in the test bench; not synthesizable
	byte dynamicArray[];
	byte queue[$];
	byte associativeArray[*];
endmodule

module test();
	int array[]; //dynamic array
	initial begin
		array = new[2]; //two elements are created
		array[0] = 12;
		array[1] = 34;
		array = new[5]; //the old one will be lost
		array = new[5](array); //now the old one will be copied over
		array.delete(); // delete all the elements
	end
endmodule

module test();
	int array[$]; //queue
	initial begin
		array.push_back(12);
		array.push_front(34); //it will change indexes of previously created elements
		array.pop_front();
		array.pop_back();
		array.insert(1, 90); // (index of insertion, value to bne inserted)
		array.delete(1);
	end
endmodule

module test();
	int array[*]; //associate array
	initial begin
		array[10] = 12;
		array[100] = 34;
		array.delete(100);
	end
endmodule

module test();
	int array[$] = '{1,2,2,3,3,3};
	int result; int resultQ[$];
	initial begin
		result = array.size(); // 6
		result = array.sum(); // 14
		resultQ = array.min(); // '{1} - minimal value in the array
		resultQ = array.max();
		resultQ = array.unique();
	end
endmodule
```

## 9. Function and Task
```Verilog
// No Function
module test();
	int a, b, result;
	initial begin
		a=1; b=2; result=a+b;
		a=3; b=4; result=a+b;
		a=5; b=6; result=a+b;
	end
endmodule

// Simple Function
module test();
	int a, b, result;
	function void calculate();
		result = a + b;
	endfunction
	initial begin
		a=1; b=2; calculate();
		a=3; b=4; calculate()'
		a=5; b=6; calculate();
	end
endmodule

// Function $ arguments
module test();
	int a, b, result;
	function void calculate(int a, b);
		result = a + b;
	endfunction
	initial begin
		calculate(1, 2);
		calculate(3, 4)'
		calculate(5, 6);
	end
endmodule

// Function $ return value
module test();
	int a, b, result;
	function int calculate(int a, b);
		return(a + b);
	endfunction
	initial begin
		#1ns; result = calculate(1, 2);
		#1ns; result = calculate(3, 4)'
		#1ns; result = calculate(5, 6);
	end
endmodule
```
![[Pasted image 20240427205839.png|500]]
`function` - support `return` value, but `task` does not !
`task` - can have delay and timing control, but `function` cannot !
```Verilog
module test();
	int result;
	task calculate(int a, b)'
		#1ns; result = (a + b)
	endtask

	initial begin
		calculate(1, 2);
		calculate(3, 4);
		calculate(5, 6);
	end
endmodule
```
`function` cannot call `task`, but `talks` can call `function`

```verilog
// different ways of defining and calling
function void fsimple(a, b);
function void fsimple(int a, b);
function void fsimple(int a, int b);
function void fsimple(int a, int b=1); //default value

fsimple(0, 1);
fsimple(.a(0), .b(1));
```

## 9a. Function and Task Argument Direction
```verilog
task myTask(input logic a); // default is input, can be also output or inout
	$display("a=%0d", a);
endtask

initial begin
	myTask(1);
end
```
![[Pasted image 20240427211139.png|400]]
![[Pasted image 20240427211235.png|400]]
![[Pasted image 20240427211302.png|400]]

passing by a reference
![[Pasted image 20240427211350.png|400]]

## 10. Threads
```verilog
module test();
	initial begin
		fork
			#10ns $display("Doing A");
			#20ns $display("Doingm B");
		join
		$display("Doing C");
	end
endmodule
```
![[Pasted image 20240427211825.png|400]]
![[Pasted image 20240427211902.png|400]]
![[Pasted image 20240427211938.png|400]]
```verilog
module test (input logic reset);
	initial begin
		fork
			@(posedge reset);
			#100ns $error("Timeout");
		join_any
		disable fork;
	end
endmodule

module test (input logic reset);
	initial begin
		fork
			@(posedge reset);
			#100ns $error("Timeout");
		join_none
		disable fork;
	end
endmodule
```
![[Pasted image 20240427212009.png|400]]
![[Pasted image 20240427212332.png|400]]
some tricks that you may want to do with `join_none`
![[Pasted image 20240427212513.png|400]]
![[Pasted image 20240427212538.png|400]]
important to use `automatic` keywork in this case !
![[Pasted image 20240427212730.png|400]]

## 11. Events
```verilog
@( clk ); // any changes
@( posedge clk );
@( negedge clk );
@( posedge clk or negedge rest );
@( posedge clk, negedge reset );

initial begin
	wait (clk);
	wait (clk == 1'b1);
end
```
![[Pasted image 20240427213228.png|400]]
```verilog
event ev;
initial begin
	->(ev); //Trigger an event
	@(ev); // Wait for an event\
	wait(ev.triggered);
end

module test(
	input logic clk;
	input logic[7:0] a, b;
	output logic[7:0] o;
);
	event ev;
	always @(posedge clk) begin
		o = a | b;
		if (a == 8'hff b == 8'hff) begin
			-> ev;
		end 
	end
endmodule
```
![[Pasted image 20240427214817.png|400]]
```verilog
module test();
	event ev;
	initial begin
		fork
			// two thread
			#10ns -> (ev); // triggering
			#20ns @(ev); // awaiting
		join
	end
endmodule
```
![[Pasted image 20240427214924.png|400]]

## 12. Class Basic
encapsulation 
dynamic initialization
abstraction
pointer
inheritance
polymorphism
```verilog
module test(); //type + build-in function
	string str;
	int q[$]; //queue
	initial begin
		s = "Hello";
		s = s.toUpper();
		q.push_back(1);
		q.push_back(3);
		$display("%0d", q.sum());
	end
endmodule
```
```verilog
class cStaticsCalculator;
	int q[$];
	function int getMean();
		return (q.sum()/q.size());
	endfunction
endclass
```
```verilog
module test();
	cStaticsCalculator sc;

	initial begin
		sc = new();
		sc.push_back(1);
		sc.push_back(3);
		$display("%0d", sc.getMean());
endmodule
```
```verilog
class cNumber; //new constructor
	byte b;
	function new();
		b = 1;
	endfunction 
	// alternatively
	function new(byte argB);
		b = argB;
	endfunction 
endclass

module test();
	cNumber = cn;
	initial begin
		cn = new(10);
		cn.b = 20;
endmodule
```
![[SystemVerilog Tutorial in 5 Minutes - 12 Class Basic 4-20 screenshot.png|500]]![[Pasted image 20240511200412.png|500]]
Class variable / handle can be initialized dynamically

## 12a. Class Members Attribute 
```verilog
int life = 3;
local int life = 3;
static int count = 0
static bit debug = 0;

function int getLife();
	return life;
endfunction

function new(); //class counter example
	count ++;
endfunction

cNumber::debug = 1;

int id;
function new(int id);
	this.id = idl
endfunction
```
## 12b. Class Pointer
![[Pasted image 20240511201349.png|500]]


## 12c. Class Randomization
```verilog
repeat (3) begin
	b = $random();
	b = $urandom_range(50, 1);
end 

class cNumber;
	rand bit b;
	rand byte by;
	constraint cst {
		by > 0;
		by < 3;
		by == 1;
		by != 2;
	}
	constraint cst2 {
		by inside {0,1,2,3};
		by inside {[0:3]};
		by dist {0:=4,[1:3]:=2}
		by dist {0:/4,[1:3]:/2}
	}
endclass

cNumber cn;
initial begin
	cn = new()
	repeat (6) begin
		cn.randomize();
	end
end

cn.randmize();
cn.b.rabd_mode(0);
cn.cst.constraint_mode(0);
```
## 12d. Class Inheritance
```verilog
class ecNumber extends cNumber;
// adding new stuff to the class
function void print();
	super.print();
	if (i<0) $error("i less than 0");
endclass 
```
## 12e. Class Polymorphism
![[Pasted image 20240511215417.png|500]]![[Pasted image 20240511215636.png|500]]

## 13. Covergroup and Coverpoint
![[Pasted image 20240511220236.png|500]]

## 13a\. Coverpoint bins
## 14. Interface
```verilog
module dut_tx(input logic clk, output logic[2:0] a);
	always @(posedge clk) a <= $random;
end module

module dut_rx(input logic clk, input logic[2:0] a);
	always @(posedge clk) $display("%0b", a);
endmodule

module test();
	logic clk;
	always #5ps clk~=clk;
	logic[2:0] a;
	dut_tx dt(clk, a);
	dut_rx dr(clk, a);
endmodule

interface intf(input logic clk);
	logic[2:0] a;
endinterface

module test();
	logic clk;
	always #5ps clk~=clk;
	intf i(clk);
	dut_tx dt( i.clk, i.a);
	dut_rx dr( i.clk, i.a);
endmodule
```
![[Pasted image 20240512134911.png|500]]


## 15. Virtual Interface
![[Pasted image 20240512135143.png|500]]
![[Pasted image 20240512135347.png|500]]
![[Pasted image 20240512135702.png]]

Virtual interface is used by class to send / receive signals from an actual interface
## 16. Program & Scheduling
![[Pasted image 20240512135842.png]]

Program block is used to write test (as a differentiation from a design module). It has its own scheduling region.

## 16a. Non Blocking Assignment
