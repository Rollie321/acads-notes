**Run a test bench with for loop**
```verilog

`timescale 1ns / 1ps
// use time scale to scale up the time axis to make 
// #1 convert into 1ns instead of 1ps

module name();
	reg A, B, C, D;
	wire X;

	main DUT (A, B, C, D, X);

	integer i;

	initial begin
		// declare the for loop
		for(i = 0; i < 16; i = i + 1) begin
			// assign the A, B, C, D to the binary digits of i
			{A, B, C, D} = i[3:0];

			#1;

			$display("%b %b %b", A, B, C)
		end

		$finish;
	end
endmodule
```

**Run a test bench with even input of numbers**

```verilog

`timescale 1ns / 1ps
// use time scale to scale up the time axis to make 
// #1 convert into 1ns instead of 1ps

module name();
	reg A, B, C, D;
	wire X;

	main DUT (A, B, C, D, X);

	integer i;

	initial begin
		// declare the for loop
		for(i = 0; i < 16; i = i + 1) begin

			if (i % 2 == 0) begin
				// assign the A, B, C, D to the binary digits of i
				{A, B, C, D} = i[3:0];
				#1;
				$display("%b %b %b", A, B, C)		
			end

		end

		$finish;
	end
endmodule
```