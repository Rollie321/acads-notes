Main file:
```verilog
module decoder24 (a,b);
	input [1:0] a; // declare a as 2 bit
	output [3:0]b; // declare b as 4 bit
	reg [3:0] b; // declare b as reg type so it can be reassigned later

	always @(a) // set event listener whenever value of a changes
	begin // set each bit of output b based on bits of given input a
		b[3] = a[1] & a[0];
		b[2] = !a[1] & a[0];
		b[1] = a[1] & !a[0];
		b[0] = !a[1] & !a[0];
	end
endmodule
```


Test Bench:
```verilog
	
module  decoder_tst();
	reg [1:0] a;
	wire [3:0] b;
	
	decoder24 uut ( .a(a), .b(b) );
	// this is the format for UUT
	// however DUT can also be used but the parameters will be
	// the same as before
	
	initial begin
		a = 2'b00;	
		#100 a = 2'b01;
		#100 a = 2'b10;
		#100 a = 2'b11;
	end
endmodule
```

