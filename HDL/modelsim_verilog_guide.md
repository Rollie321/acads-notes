# Compiling and Running Verilog with ModelSim (Terminal)

This guide shows how to compile and simulate a Verilog design using ModelSim's command-line tools (`vlib`, `vlog`, `vsim`) instead of the GUI.

## Prerequisites

- ModelSim installed, with its `bin` directory added to your `PATH` (or you reference the full path to the binaries directly).
- To add it to your PATH for the current terminal session (Linux/macOS example):

```bash
export PATH=$PATH:/path/to/modelsim/bin
```

On Windows (Command Prompt):

```cmd
set PATH=%PATH%;C:\modeltech\win32
```

Verify the tools are reachable:

```bash
vlog -version
vsim -version
```

## Sample Verilog File

Save the following as `hello.v`. It is a simple testbench that prints a message and a counting sequence to the terminal.

```verilog
// hello.v
module hello_tb;

  integer i;

  initial begin
    $display("Hello from ModelSim!");
    $display("Starting count sequence...");

    for (i = 0; i < 5; i = i + 1) begin
      $display("Count = %0d", i);
      #10; // wait 10 time units
    end

    $display("Simulation finished.");
    $finish;
  end

endmodule
```

## Step-by-Step: Compile and Run

Run these commands from the terminal, in the directory containing `hello.v`.

### 1. Create a working library

```bash
vlib work
```

This creates a `work` directory that ModelSim uses to store compiled design units.

### 2. Compile the Verilog file

```bash
vlog hello.v
```

If compilation succeeds, you'll see messages like:

```
-- Compiling module hello_tb
Top level modules:
	hello_tb
```

### 3. Run the simulation in the terminal (no GUI)

Use `vsim` in command-line mode with the `-c` flag, and run to completion with `-do`:

```bash
vsim -c hello_tb -do "run -all; quit"
```

- `-c` runs ModelSim in console (non-GUI) mode.
- `-do "run -all; quit"` tells the simulator to run the simulation to completion and then exit automatically.

### Expected Output

```
# Hello from ModelSim!
# Starting count sequence...
# Count = 0
# Count = 1
# Count = 2
# Count = 3
# Count = 4
# Simulation finished.
```

## One-Line Version

You can combine everything into a single command if you want to compile and run in one go:

```bash
vlib work && vlog hello.v && vsim -c hello_tb -do "run -all; quit"
```

## Optional: Cleaning Up

To remove the compiled library and start fresh:

```bash
vdel -lib work -all
```

## Notes

- `$display` prints immediately when executed (like `printf`), while `$monitor` prints whenever any of its listed signals change — useful for watching signal values over time.
- If your design has multiple files, compile them all before simulating, e.g. `vlog file1.v file2.v file3.v`, or use a filelist: `vlog -f filelist.txt`.
- Replace `hello_tb` with the name of your actual top-level module/testbench when adapting this to your own project.
