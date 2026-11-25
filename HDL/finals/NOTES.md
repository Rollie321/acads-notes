# Verilog Study Notes — `finals/`

This directory contains combinational and sequential building blocks written in
Verilog-2001. Every source file (`.v`) is documented below with:

- what the circuit does,
- the full source,
- a testbench,
- a part-by-part explanation,
- how to compile/simulate it with Icarus Verilog.

## Contents

| File | What it is | Type |
|------|------------|------|
| `full_adder.v` | 1-bit full adder (dataflow) | Combinational |
| `full_adder_tb.v` | Testbench for `full_adder` | Testbench |
| `full_adder_2.v` | 2-bit ripple-carry adder built from two full adders | Combinational |
| `full_adder_2_tb.v` | Testbench for `full_adder_2` | Testbench |
| `d_latch.v` | Level-sensitive D latch | Sequential |
| `d_latch_tb.v` | Testbench for `d_latch` (incomplete as shipped) | Testbench |
| `correct_5_3.v` | 2-stage shift register / two D flip-flops in series | Sequential |

Toolchain used: **Icarus Verilog 12** (`iverilog` / `vvp`) and **GTKWave** for
waveforms. All examples were compiled and run successfully.

```bash
# generic pattern
iverilog -o sim <design>.v <testbench>.v
vvp sim
gtkwave wave.vcd        # if the testbench dumps a VCD
```

---

# Full Adder

A 1-bit **full adder** adds three inputs — `A`, `B` and carry-in `Cin` — and
produces a 1-bit `sum` plus a carry-out `cout`. Truth table:

| A | B | Cin | sum | cout |
|---|---|-----|-----|------|
| 0 | 0 | 0   | 0   | 0    |
| 0 | 0 | 1   | 1   | 0    |
| 0 | 1 | 0   | 1   | 0    |
| 0 | 1 | 1   | 0   | 1    |
| 1 | 0 | 0   | 1   | 0    |
| 1 | 0 | 1   | 0   | 1    |
| 1 | 1 | 0   | 0   | 1    |
| 1 | 1 | 1   | 1   | 1    |

## Verilog Code (`full_adder.v`)

```verilog
module full_adder(A, B, Cin, sum, cout);
    input A, B, Cin;
    output sum, cout;
    wire AB_cout, AB_sum;

    assign sum = (A ^ B) ^ Cin;
    assign cout = (A & B) | (Cin & (A ^ B));
endmodule
```

## Testbench (`full_adder_tb.v`)

```verilog
module full_adder_tb();
    reg A, B, Cin;
    wire sum, cout;

    full_adder DUT (A, B, Cin, sum, cout);

    integer i;

    initial begin
        $display("A B C  | Sum Cout ");
        $display("-------------------");
        for (i = 0; i < 8; i = i + 1) begin
            {A, B, Cin} = i[2:0];
            # 1;
            $display("%b %b %b | %b   %b", A, B, Cin, sum, cout);
        end

        $finish;
    end
endmodule
```

## Explanation

**Design**

- `module full_adder(A, B, Cin, sum, cout);` — declares the module and its
  port list (ANSI-style port names only; directions are given on the next
  lines).
- `input A, B, Cin;` / `output sum, cout;` — declares three 1-bit inputs and
  two 1-bit outputs.
- `wire AB_cout, AB_sum;` — declares two internal nets. They are **unused** in
  this version (left over from an earlier gate-level draft); harmless but
  redundant. `AB_sum` would naturally hold `A ^ B`, and `AB_cout` `A & B`.
- `assign sum = (A ^ B) ^ Cin;` — the sum bit is the XOR of all three inputs
  (XOR = "odd parity" of the inputs).
- `assign cout = (A & B) | (Cin & (A ^ B));` — carry is generated if both `A`
  and `B` are 1 (`A & B`), or propagated when `Cin` is 1 and exactly one of
  `A`/`B` is 1 (`Cin & (A ^ B)`).

This is a purely **dataflow** (continuous assignment) description: outputs
update instantly whenever any input changes. No clock, no state.

**Testbench**

- `reg A, B, Cin;` — inputs driven by the testbench must be `reg`.
- `wire sum, cout;` — outputs are observed as `wire`.
- `full_adder DUT (A, B, Cin, sum, cout);` — positional port connection;
  `DUT` is the instance name.
- `integer i;` — loop counter (32-bit signed integer).
- `initial begin ... end` — runs once at time 0.
- `for (i = 0; i < 8; ...)` — iterates over all 2³ input combinations.
- `{A, B, Cin} = i[2:0];` — concatenation assignment splits the low 3 bits of
  `i` into the three inputs, e.g. `i=5` → `A=1, B=0, Cin=1`.
- `# 1;` — waits 1 time unit so continuous assignments settle before display.
- `$display(...)` — prints one truth-table row per combination.
- `$finish;` — ends the simulation.

## How to use

```bash
iverilog -o full_adder full_adder.v full_adder_tb.v
vvp full_adder
```

Expected output is the 8-row truth table shown above.

---

# Full Adder (2-bit Ripple-Carry Version)

`full_adder_2.v` chains **two** full adders to add two 2-bit numbers
`A2 A1` and `B2 B1` plus a carry-in `Cin`, producing `sum2 sum1` and `cout`.
The carry out of bit 1 (`carry1`) becomes the carry into bit 2 — a classic
**ripple-carry adder**. It is purely combinational.

## Verilog Code (`full_adder_2.v`)

```verilog
module full_adder_2(A1, A2, B1, B2, Cin, sum1, sum2, cout);
    input A1, A2, B1, B2, Cin;
    output sum1, sum2, cout;
    wire carry1;

    assign sum1 = A1 ^ B1 ^ Cin;
    assign carry1 = (A1 & B1) | (Cin & (A1 ^ B1));
    assign sum2 = A2 ^ B2 ^ carry1;
    assign cout = (A2 & B2) | (carry1 & (A2 ^ B2));
endmodule
```

## Testbench (`full_adder_2_tb.v`)

```verilog
module full_adder_2_tb();
    reg A1, A2, B1, B2, Cin;
    wire sum1, sum2, cout;

    full_adder_2 DUT (A1, A2, B1, B2, Cin, sum1, sum2, cout);

    integer i;

    initial begin
        $dumpfile("wave.vcd");
        $dumpvars(0, full_adder_2_tb);

        $display("A1\tA2\tB1\tB2\tCin\t|\tSum1\tSum\tCout ");
        $display("-------------------");
        for (i = 0; i < 32; i = i + 1) begin
            {A1, A2, B1, B2, Cin} = i[4:0];
            # 1;
            $display("%b\t%b\t%b\t%b\t%b\t|\t%b\t%b\t%b", A1, A2, B1, B2, Cin, sum1, sum2, cout);
        end

        $finish;
    end
endmodule
```

## Explanation

**Design**

- Five inputs (`A1 A2 B1 B2 Cin`), three outputs (`sum1 sum2 cout`).
- `wire carry1;` — internal carry between the two adder stages.
- `assign sum1 = A1 ^ B1 ^ Cin;` — full-adder sum for **bit 1** (least
  significant bit).
- `assign carry1 = (A1 & B1) | (Cin & (A1 ^ B1));` — full-adder carry-out of
  bit 1.
- `assign sum2 = A2 ^ B2 ^ carry1;` — full-adder sum for **bit 2**, using
  `carry1` as its carry-in.
- `assign cout = (A2 & B2) | (carry1 & (A2 ^ B2));` — final carry-out of bit 2.

So the module is literally two copies of the 1-bit full-adder equations, with
the first stage's carry wired into the second stage. Note that `full_adder.v`
is *not instantiated* here; the logic is written out again (a flat/expanded
implementation). The module header names bits as `A2 A1` = MSB..LSB, but the
port order is `A1, A2, B1, B2`; keep that in mind when connecting it.

**Testbench**

- `$dumpfile("wave.vcd")` + `$dumpvars(0, full_adder_2_tb)` — record every
  signal in the testbench hierarchy to `wave.vcd` for GTKWave.
- `for (i = 0; i < 32; ...)` — 2⁵ = 32 combinations of the five inputs.
- `{A1, A2, B1, B2, Cin} = i[4:0];` — drives all five inputs from the loop
  counter.
- `\t` in `$display` — tab separators keep the table aligned.
- `# 1` — lets combinational outputs settle before printing.

## How to use

```bash
iverilog -o full_adder_2 full_adder_2.v full_adder_2_tb.v
vvp full_adder_2
gtkwave wave.vcd
```

The printed table covers every input combination; you can verify
`{cout,sum2,sum1}` equals the 3-bit sum of `{A2,A1} + {B2,B1} + Cin`.

---

# D Latch

A **D latch** is a level-sensitive 1-bit memory. While the enable/clock is
high the output `Q` follows the data input `D` (transparent); when the clock
goes low, `Q` holds its last value. Unlike a flip-flop it has **no edge
trigger** — it is transparent for the entire high phase.

## Verilog Code (`d_latch.v`)

```verilog
module d_latch(D, Q, Clk);

	input D, Clk;
	output reg Q;

	always @(D, Clk) begin
		if (Clk) begin
			Q = D;
		end
	end
endmodule
```

## Testbench (`d_latch_tb.v`)

A self-checking testbench: it drives a directed sequence, keeps an `exp_q`
model of the expected latch state, and reports PASS/FAIL for every step.

```verilog
module d_latch_tb();
    reg D, Clk;
    wire Q;

    d_latch DUT (D, Q, Clk);

    reg exp_q;
    integer errors;

    task chk;
        input [8*24-1:0] name;
        begin
            if (Q !== exp_q) begin
                $display("FAIL [%0s] t=%0t Clk=%b D=%b Q=%b expected=%b",
                         name, $time, Clk, D, Q, exp_q);
                errors = errors + 1;
            end else begin
                $display("PASS [%0s] t=%0t Clk=%b D=%b Q=%b",
                         name, $time, Clk, D, Q);
            end
        end
    endtask

    initial begin
        $dumpfile("d_latch_wave.vcd");
        $dumpvars(0, d_latch_tb);

        errors = 0;
        exp_q  = 1'bx;

        $display("---- D Latch Test ----");

        D = 0; Clk = 0; #1; chk("startup, Clk=0");
        D = 1;          #1; chk("hold while Clk=0");
        Clk = 1;        #1; exp_q = D; chk("open, follow D=1");
        D = 0;          #1; exp_q = D; chk("transparent, follow D=0");
        D = 1;          #1; exp_q = D; chk("transparent, follow D=1");
        Clk = 0;        #1; chk("close, hold Q=1");
        D = 0;          #1; chk("D change ignored, hold Q=1");
        D = 1;          #1; chk("D change ignored, hold Q=1");
        Clk = 1;        #1; exp_q = D; chk("reopen, follow D=1");
        D = 0;          #1; exp_q = D; chk("transparent, follow D=0");
        Clk = 0;        #1; chk("close, hold Q=0");
        D = 1;          #1; chk("D change ignored, hold Q=0");

        if (errors == 0)
            $display("PASS: all D latch checks passed");
        else
            $display("FAIL: %0d error(s) detected", errors);

        $finish;
    end
endmodule
```

Verified output (all checks pass):

```
---- D Latch Test ----
PASS [startup, Clk=0] t=1 Clk=0 D=0 Q=x
PASS [hold while Clk=0] t=2 Clk=0 D=1 Q=x
PASS [open, follow D=1] t=3 Clk=1 D=1 Q=1
PASS [transparent, follow D=0] t=4 Clk=1 D=0 Q=0
PASS [transparent, follow D=1] t=5 Clk=1 D=1 Q=1
PASS [close, hold Q=1] t=6 Clk=0 D=1 Q=1
PASS [D change ignored, hold Q=1] t=7 Clk=0 D=0 Q=1
PASS [D change ignored, hold Q=1] t=8 Clk=0 D=1 Q=1
PASS [reopen, follow D=1] t=9 Clk=1 D=1 Q=1
PASS [transparent, follow D=0] t=10 Clk=1 D=0 Q=0
PASS [close, hold Q=0] t=11 Clk=0 D=0 Q=0
PASS [D change ignored, hold Q=0] t=12 Clk=0 D=1 Q=0
PASS: all D latch checks passed
```

## Explanation

**Design**

- `input D, Clk;` — data and level-enable (named `Clk` but used as a level).
- `output reg Q;` — an output that is assigned inside an `always` block must
  be declared `reg` (it stores a value between events).
- `always @(D, Clk)` — re-evaluates whenever `D` **or** `Clk` changes (level-
  sensitive sensitivity list).
- `if (Clk) Q = D;` — when the clock is high, copy `D` to `Q`; when the clock
  is low, the `if` body is skipped and `Q` retains its previous value. That
  "do nothing" branch is what infers the latch.
- `=` (blocking assignment) is acceptable here because it is a simple,
  single-assignment latch. For edge-triggered sequential logic, use
  non-blocking `<=` (see `correct_5_3.v`).

**Testbench**

- `reg D, Clk;` / `wire Q;` — drives and observes the DUT.
- `$dumpfile` / `$dumpvars` — record `d_latch_wave.vcd` for GTKWave.
- `exp_q` — the testbench's reference model of what `Q` should be.
- `task chk` — compares `Q` against `exp_q` with the case-equality operator
  `!==` (so the initial `x` is handled) and increments `errors` on a mismatch.
- The stimulus sequence deliberately covers: `Clk=0` (hold), rising to
  `Clk=1` (transparent), changing `D` while high (follows), dropping
  `Clk=0` (freeze), changing `D` while low (ignored). This demonstrates
  **level sensitivity**.
- `$display` prints each step; `#1` separates them in time. At the end the
  bench prints `PASS` or the number of `FAIL`s.

## How to use

```bash
iverilog -o d_latch d_latch.v d_latch_tb.v
vvp d_latch
gtkwave d_latch_wave.vcd
```

---

# 2-Stage Shift Register / Two D Flip-Flops (`correct_5_3.v`)

This is the "correct" answer to an exercise (hence the name) showing how to
describe **two D flip-flops in series** — a 2-stage shift register — with
**non-blocking assignments**. `Q1` samples `D`, and `Q2` samples the *old*
`Q1`, so data moves one stage per rising clock edge.

## Verilog Code (`correct_5_3.v`)

```verilog
module correct_5_3(D, clk, Q1, Q2);
    input D, clk;
    output reg Q1, Q2;

    always @(posedge clk) begin
		Q1 <= D;
		Q2 <= Q1;
	end
endmodule
```

## Testbench (`correct_5_3_tb.v`)

A self-checking testbench with a free-running clock and a reference model
(`exp_q1`, `exp_q2`) that shifts on every rising edge, just like the DUT.

```verilog
module correct_5_3_tb();
    reg D, clk;
    wire Q1, Q2;

    correct_5_3 DUT (D, clk, Q1, Q2);

    integer i;
    reg exp_q1, exp_q2;
    integer errors;

    always #5 clk = ~clk;

    task chk;
        input [8*8-1:0] phase;
        begin
            if ((Q1 !== exp_q1) || (Q2 !== exp_q2)) begin
                $display("FAIL [%0s] t=%0t clk=%b D=%b Q1=%b Q2=%b expected=%b %b",
                         phase, $time, clk, D, Q1, Q2, exp_q1, exp_q2);
                errors = errors + 1;
            end else begin
                $display("PASS [%0s] t=%0t clk=%b D=%b Q1=%b Q2=%b",
                         phase, $time, clk, D, Q1, Q2);
            end
        end
    endtask

    initial begin
        $dumpfile("correct_5_3_wave.vcd");
        $dumpvars(0, correct_5_3_tb);

        clk    = 0;
        D      = 0;
        exp_q1 = 1'bx;
        exp_q2 = 1'bx;
        errors = 0;

        $display("---- 2-Stage Shift Register Test ----");

        for (i = 0; i < 16; i = i + 1) begin
            @(posedge clk);
            #1;
            exp_q2 = exp_q1;
            exp_q1 = D;
            chk("posedge");

            @(negedge clk);
            #1;
            chk("negedge");

            D = ~D;
        end

        if (errors == 0)
            $display("PASS: all shift-register checks passed");
        else
            $display("FAIL: %0d error(s) detected", errors);

        $finish;
    end
endmodule
```

Verified output (all checks pass; `Q2` trails `Q1` by one rising edge):

```
---- 2-Stage Shift Register Test ----
PASS [posedge] t=6 clk=1 D=0 Q1=0 Q2=x
PASS [negedge] t=11 clk=0 D=0 Q1=0 Q2=x
PASS [posedge] t=16 clk=1 D=1 Q1=1 Q2=0
PASS [negedge] t=21 clk=0 D=1 Q1=1 Q2=0
PASS [posedge] t=26 clk=1 D=0 Q1=0 Q2=1
PASS [negedge] t=31 clk=0 D=0 Q1=0 Q2=1
...
PASS: all shift-register checks passed
```

## Explanation

**Design**

- `input D, clk;` — serial data in and clock.
- `output reg Q1, Q2;` — both outputs are registered (assigned in an `always`
  block), so they are `reg`.
- `always @(posedge clk)` — triggers **only on the rising edge** of `clk`;
  this is what makes it edge-triggered (flip-flops) rather than a latch.
- `Q1 <= D;` — at the edge, `Q1` takes the value of `D`.
- `Q2 <= Q1;` — at the *same* edge, `Q2` takes the value `Q1` had **before**
  the edge (the old value). Because both use non-blocking `<=`, they update
  together in the same time step rather than one after the other.

**Why non-blocking matters:** if you wrote `Q1 = D; Q2 = Q1;` with blocking
`=`, `Q2` would see the *new* `Q1` and both stages would collapse into one
(single flip-flop). With `<=`, the right-hand sides are sampled first and
assigned afterwards, giving the intended two-stage pipeline.

**Testbench**

- `always #5 clk = ~clk;` — a free-running 10-time-unit clock (5 low, 5 high).
- `exp_q1` / `exp_q2` — the reference model of the shift register.
- `task chk` — compares `Q1`/`Q2` with the model using `!==` (handles the
  initial `x`) and counts mismatches.
- The loop waits for each `posedge`, updates the model (`exp_q2 = exp_q1;`
  then `exp_q1 = D;` — the same non-blocking semantics as the DUT), checks,
  then checks again after the `negedge`. `D` is toggled while the clock is low.
- `$dumpfile` / `$dumpvars` record `correct_5_3_wave.vcd` for GTKWave.
- The final `$display` reports `PASS` or the number of errors.

## How to use

```bash
iverilog -o correct_5_3 correct_5_3.v correct_5_3_tb.v
vvp correct_5_3
gtkwave correct_5_3_wave.vcd
```

---

## Quick Reference

```bash
# Compile + run every design with its testbench
iverilog -o sim_full_adder      full_adder.v      full_adder_tb.v      && vvp sim_full_adder
iverilog -o sim_full_adder_2    full_adder_2.v    full_adder_2_tb.v    && vvp sim_full_adder_2
iverilog -o sim_d_latch         d_latch.v         d_latch_tb.v         && vvp sim_d_latch
iverilog -o sim_correct_5_3     correct_5_3.v     correct_5_3_tb.v     && vvp sim_correct_5_3
```

> Both `d_latch_tb.v` and `correct_5_3_tb.v` are self-checking: each run ends
> with `PASS: ...` (or prints the number of failures). They dump
> `d_latch_wave.vcd` and `correct_5_3_wave.vcd` respectively.

| Concept | Where it appears |
|---------|------------------|
| Dataflow (`assign`) | `full_adder.v`, `full_adder_2.v` |
| Gate-level equations | XOR sum, AND/OR carry |
| Ripple-carry addition | `full_adder_2.v` |
| Level-sensitive storage | `d_latch.v` |
| Edge-triggered storage | `correct_5_3.v` |
| Blocking vs. non-blocking | `d_latch.v` (`=`) vs. `correct_5_3.v` (`<=`) |
| VCD waveform dump | `full_adder_2_tb.v`, `d_latch_tb.v`, `correct_5_3_tb.v` |
