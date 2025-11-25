# Computer Organization and Networks

- [CON](#con)
  - [System Verilog](#system-verilog)
    - [Syntax \& Data types](#syntax--data-types)
    - [Number Representation](#number-representation)
    - [Operations](#operations)
    - [Implementation Styles](#implementation-styles)
      - [Structural](#structural)
      - [Behavioral](#behavioral)
    - [Sequential \& Combinational Circuit](#sequential--combinational-circuit)
      - [Combinational Logic](#combinational-logic)
      - [Sequential Logic](#sequential-logic)
      - [Storage Elements](#storage-elements)
      - [Sensitivity list \& posedge](#sensitivity-list--posedge)
      - [Common Pitfalls](#common-pitfalls)
    - [Coding Style](#coding-style)
    - [Finite State Machines (FSM)](#finite-state-machines-fsm)
  - [Communication Interface in System Verilog](#communication-interface-in-system-verilog)
    - [System Verilog Details](#system-verilog-details)
    - [FSM with separate data path](#fsm-with-separate-data-path)
    - [Examples](#examples)
    - [Analysis](#analysis)
  - [Describing Combinational Circuits](#describing-combinational-circuits)
    - [Clock Frequency](#clock-frequency)
  - [State Machines](#state-machines)
    - [Finite State Machines (FSM)](#finite-state-machines-fsm-1)
    - [Datapath](#datapath)
    - [Algorithmic State Machines](#algorithmic-state-machines)
  - [Processors](#processors)
    - [Arithmetic Logic Unit (ALU)](#arithmetic-logic-unit-alu)
    - [Register File](#register-file)
    - [Instruction Register](#instruction-register)
      - [Instruction Set Architecture (ISA)](#instruction-set-architecture-isa)
      - [RISC-V Instruction Sets](#risc-v-instruction-sets)
        - [Integer Computational Instructions](#integer-computational-instructions)
    - [Memory](#memory)
    - [Instruction Memory](#instruction-memory)
    - [Interfacing with I/O Devices](#interfacing-with-io-devices)
  - [Programming a RISC-V CPU](#programming-a-risc-v-cpu)
    - [Function Calls](#function-calls)
      - [Calling Convention](#calling-convention)
  - [Networking](#networking)
    - [TCP/IP model](#tcpip-model)
      - [Link layer](#link-layer)
        - [Ethernet](#ethernet)
      - [Network Layer](#network-layer)
        - [IPv4](#ipv4)
        - [IPv6](#ipv6)
        - [Network Address Translation](#network-address-translation)
      - [Transport Layer](#transport-layer)
        - [UDP](#udp)
        - [TCP](#tcp)
      - [Application Layer](#application-layer)
        - [DNS](#dns)
        - [NTP](#ntp)
        - [SSH](#ssh)
        - [BGP](#bgp)
        - [HTTP](#http)
  - [Memory Systems](#memory-systems)
    - [Cache](#cache)
      - [Cache Design](#cache-design)
      - [Read Operation](#read-operation)
      - [Replacement Policies](#replacement-policies)
      - [Write Operations](#write-operations)
      - [Which Cache to use where?](#which-cache-to-use-where)
    - [Virtual Memory](#virtual-memory)
    - [Combining Virtual Memory \& Caches](#combining-virtual-memory--caches)
    - [Processor Design](#processor-design)
    - [Pipelining](#pipelining)
      - [Data Dependence Handling](#data-dependence-handling)
      - [Control Hazards](#control-hazards)
      - [Register Renaming](#register-renaming)
  - [Interrupts \& Multitasking](#interrupts--multitasking)
    - [Communcation via a Slow Interface](#communcation-via-a-slow-interface)
    - [Handling unexpected external events](#handling-unexpected-external-events)
      - [Interrupts in RISC-V](#interrupts-in-risc-v)
        - [ISR](#isr)
    - [DMA](#dma)
    - [Multitasking](#multitasking)
      - [Multiprocessor Design](#multiprocessor-design)
      - [Hardware Multithreading](#hardware-multithreading)

---

## System Verilog

### Syntax & Data types

Simple Example for System Verilog
    module circuit (
        input in_a,
        input in_b,
        input in_c,
        output out_q,
    )
    assign out_q = ((~in_a & in_b) | in_c);
    endmodule

always_comb -> should always be true when input changes

Verilog if Statements
    always_comb begin
        if (in_a == 0)
            ...
        else
            ...
    end

Logic type
- data type in System Verilog
- can have multiple bits [(MSB) Most Significant Bit : (LSB) Least Signifcant Bit]
- represent wires in code
  - for connections & to model registers
  - for in- / output ports
- "signed" keyword (logic per default unsigned)

Logic type example
  logic one_bit; // 1 bit wire
  logic [1:0] two_bit; // 2 bit wire
  logic [7:0] eight_bit; // 8 bit wire

  one_bit = 1'b0;

  eight_bit = 7'b01011001;

  // equivalent to

  eight_bit[0] = 0;
  eight_bit[1] = 1;
  eight_bit[2] = 0;
  eight_bit[3] = 1;
  eight_bit[4] = 1;
  eight_bit[5] = 0;
  eight_bit[6] = 0;
  eight_bit[7] = 1;

Logic type signed
  logic signed [3:0] four_bit_s; // signed 4 bit

Conversion to signed
  four_bit_s = $signed(four_bit);

assign
- used only outside always-blocks
- permanent wire connection
  - not a value assignment

Assign example
  assign most_significant_bit = seven_bit[6];

  // current: seven_bit[6] = 0 -> MSB = 0

  seven_bit = 7'b1101100;

  // now: seven_bit[6] = 1 -> MSB = 1

Register Arrays
- multiple x-bit registers
- assign register values with suffix _p for previous / _n for next

Register Arrays example
  logic signed [15:0] registers_p[3:0], registers_n[3:0]; // 2 arrays with 4 16bit registers
  registers_p[2] <= 16'h1234;

Enums
- set of named values
- help make code more clear & readable
- can be used with typedef

Enum example
  enum logic[1:0] {STARTING, WORKING, DONE} state_p, state_n;

  typedef enum logic[2:0] {RED, BLUE, GREEN} colors;
  colors color_p, color_n;

### Number Representation

N'Bx

- N -> number of bits
  - express hwo many bits are used to store value
- B -> base
  - b (binary), h (hexadecimal), d (decimal), o (octal)
- x -> value
  - assigned value
    - apart from numbers also possible:
      - X (indeterminate logic value)
      - Z (high impedance state)
  - underscore can be optionally used to improve readability

### Operations

- Assignment operator
  - blocking assignments: =
  - non-blocking assignments: <=
- Arithmetic operators
  - +, -, *, /, %
- Concatenating
  - using {}
- Bitwise operators
  - &, |, ^, ~, <<, >>
- Ternary operator
  - cond ? val1 : val2
- Control flow
  - if / else & case statements only allowed in always-block
- Comparisons
  - ==, !=, <, >, <=, >=
- Logical operators
  - &&, ||

Concatenating example
  logic [3:0] a;
  logic [7:0] b;

  assign b = {a[2], a[2], a[0], a[3], a[1], a[1], a[1], a[1]}

Bitwise operations example
  assign out_o = in1_i & in2_i; // AND
  assign out_o = in1_i | in2_i; // OR
  assign out_o = in1_i ^ in2_i; // XOR
  assign out_o = ~in1_i; // NOT
  assign out_o = ~(in1_i & in2_i); // NAND
  assign out_o = ~(in1_i | in2_i); // NOR

Control flow example
  always_comb begin
    if (in_i) begin
      out_o = 1'b1; end
    else begin
      out_o = 1'b0; end

    case(in_i)
      2'b00: begin
        out_o = in_i; end
      default: begin
        out_o = 2'b11; end
    endcase
  end

### Implementation Styles

#### Structural

  - Gate-Level description of circuits
  - Module contains of other modules passing inputs & outputs
    - behavior of module is specified by circuit of models
  - Hierarchial description of module instances connected by wires

Implementing other modules
  module circuit (
    input logic i1_i, i2_i, i3_i;
    output logic o1_o;
  );
    ...
  endmodule

  module structural_circuit(
    input logic a1_i, a2_i, a3_i;
    output logic x_o;
  );
    circuit inner_circuit(.i1_i(a1_i), .i2_i(a2_i), .i3_i(a3_i), .o1_o(x_o));

  endmodule

#### Behavioral

- Functional description of circuits
- Use of logical & mathematical oeprators
- Topmost abstraction layer
  - several ways to express behavioral model

Behavioral example
  module circuit (
    input logic i1_i, i2_i, i3_i;
    output logic o1_o;
  );
    logic a, b, c;

    assign a = i1_i | (~ i2_i);
    ...
  endmodule

### Sequential & Combinational Circuit

Combinational circuits
- everything happens at once
- depends on current input

Sequential circuits
- one thing happens after another
  - using combinational circuits
  - & storage elements
- output of combinational circuit is stored & used for next sequence input

#### Combinational Logic

- always_comb Block (comb -> combinational)
- defines next values for registers & outputs
- executed every time any of values read by block change (right-hand side signals)
- use blocking assignments: =
  - values assigned immediately
  - always define default behavior!
- if/else constructs & case statements are allowed inside this block
  - all left-hand side signals must be assigned in every case! (default assignment)

#### Sequential Logic

- always_ff Block (ff -> flip-flop)
- defines only register updates
- executed every time an element in sensitivity list has rising edge
  - e.g. sensitive to clk or reset changes
- use non-blocking assignments: <=
  - values are assigned at end of block
  - assignment is dine in parallel
  - always define default behavior
- if/else-construct allowed inside this block
  - to catch asynchronous reset signal
  - no more if/else or switch-case statements allowed in else

Sequential logic listening to clock / reset example
  module circuit (
    ...
  );
    ...

    always_ff @(posedge clk_i, posedge rst_i) begin
      if (rst_i == 1) begin
        ...
      end
      else begin
        ...
      end
    end
  endmodule

#### Storage Elements

- 2 different storage elements: FlipFlop/Registers & Latches
  - FlipFlop/Registers: sensitive to clock changes
  - Latches: sensitive to clock level
    - latches not used in assignments

#### Sensitivity list & posedge

- always_ff @(sensitivity list)
  - Block is executed, when event in sensitivity list is triggered
- posedge -> event is triggered as soon as signal changes from low to high

#### Common Pitfalls

- Always assign default values
  - also default statements & defaults for cases
  - default assignment (all left-hand side signals need to be assigned in every case)
- Add every needed storage element & signal to sensitivity list! (right hand side signals)
- Avoid creating latches!
  - no incomplete assignments, e.g. in combinational block an output gets not assigned under all possible input conditions

### Coding Style

- SystemVerilog is case sensitive & whitespaces are ignored
- suffix _p for previous & _n for next registers
- suffix _i for input & _o for output of modules
- use always_comb & always_ff blocks, no other always blocks (like always @(*))
- always_ff blocks only used for register updates, everything else done in combinational blocks (always_comb)
- always_ff -> non-blocking assignments (for register updates)
- always_comb -> blocking assignments (for combinational logic)
- filename should equal module name
- block always starts with keyword "begin" & ends with keyword "end"
- always use default assignments
- always use default branch for cases
- always use named assignments for module instantiations (e.g. .A(C))
- use zero based indices [MSB:LSB]

### Finite State Machines (FSM)

- next state logic (combinational logic)
  - determine next state based on current state & inputs
- state register (sequential logic)
  - store current state, load next state at rising edge
- output logic (combinational circuit)
  - generate output signals

synchronous reset -> only when clock posedge is positive
asynchronous reset -> also when just reset posedge is positive 

---

## Communication Interface in System Verilog

### System Verilog Details

Wires are driven by exactly one process
- always_comb, always_ff
- assign

Only 1 block can write to a wire, others can only read

Module instantiation
  logic module_input, module_output;
  [instantiated module name] module_i (
    .in_i(module_input),
    .out_o(module_output)
  );

**Always specify values on all code paths to prevent unwanted latches!**

### FSM with separate data path

- Create state machine to select one operation to perform
- Use registers to retain state
- Separate data path
  - Use control wires for data operations
  - separates control logic & data computation

Controller / Control Logic
- state machine that generates control signals for data path
- defines when & what action should be performed on data

Data Path
- contains all functional units & registers related to data processing
- receives control signals to perform operations on data
- provides status signals to control unit
- implements actions that should be performed

### Examples

Counter to iteratively update register
  logic [7:0] counter_n, counter_p;

  // Compute next value of register, alternatively in always_comb (combinational part)
  assign counter_n = counter_p + 8'b1;

  // Update register on clock signal (sequential part)
  always_ff @(posedge clk_i, posedge reset_i) begin
    if (reset_i) counter_p <= 8'b0;
    else counter_p <= counter_n;
  end

Shift register to shift new bits into LSB
  logic [7:0] shift_reg_n, shift_reg_p;

  assign shift_reg_n = (shift_reg_p << 1) | in_i;

  always_ff @(posedge clk_i, posedge reset_i) begin
    if (reset_i) shift_reg_p <= 8'b0;
    else shift_reg_p <= shift_reg_n;
  end

### Analysis

- GTKWave to inspect given run
  - make view, make view-comm runs tests & opens GTKWave
  - See values of wires over time
- Yosys to analyze for latches

---

## Describing Combinational Circuits

- Truth Tables
  - For small input sizes
- Logic Equation
  - for applying transformations & optimizations (logical algebra)
- Circuit Netlist
  - needed to physically build a chip
- Hardware Description Language
  - standard way of describing behavior of complex circuits

System Verilog
- widely used HDL
- old

Main Styles for Hardware Description
- Gate-Level
  - Circuits created by instantiating gates & connecting them
- Behavioral
  - Used in course & for larger circuits
  - Module body contains
    - functional description of circuit
    - logical & mathematical operators
  - Level of abstraction is higher & has many gate level realizations for behavioral description
  - For composing circuits, also structural mechanisms for composition like instantiations are used

Toolchain in Practical
- iVerilog
  - Simulator for Verilog code
- SV2V
  - Converts SystemVerilog to Verilog
- Yosys
  - Synthesis tool

Toolchain Commands
- Make
  - build -> compile code
  - run -> run simulation
  - view -> view simulation in wave viewer
  - syn -> synthesize code
  - build-syn -> compile synthesized code
  - run-syn -> run simulation based on netlist (synthesis result)
  - show -> show netlist after synthesis

Naming
- Flip-Flop: 1 bit storage sampling data on rising clock edge
- Register: n-bit storage sampling data on rising clock edge (multiple Flip-Flops)

Distance between 2 rising clock edges -> clock period

**Always use designated values for previous value & next value**

All outputs need to be defined

### Clock Frequency

Critical path
- path with longest propagation delay in comb circuit
- defines maximum clock rate

Lower temperature -> higher clock rates possible

The more transistors are switching, the more heat is produced

---

## State Machines

### Finite State Machines (FSM)

- work horses in digital systms
- we only look at synchronous FSMs
  - clock signal controls action over time
- can be described with different views
  - functional view (state diagram)
  - timing view (timing diagram)
  - structural view (logic circuit diagram)
  - behavioral view (SystemVerilog)
- synchronous FSM clocked by signal (clk)
- in each clock period, machine is in defined (current) state
- with each rising edge of clock signal, the machine advances to defined next state
- sequence of states can be defined in state transition table
- visualed by state diagrams
- act as control unit
  - generates control signals for data path

FSMs typically have outputs
- outputs are function of the state
- Moore machines
  - next state -> function of present state & input
  - output -> function of present state
- Mealy machines
  - next state -> function of present state & input
  - output -> function of present state **and input**

State encoding -> binary encoding, 2 bits to encode 3 states

Combination of 2 mealy machines may lead to an endless loop

**All digital logic can be built with Moore & Mealy Machines**  
-> However, it is not the best for every circuit

Mapping to SysVerilog
- always_comb
  - describes nextstate logic
- always_ff
  - describes state registers

### Datapath

- contains functional units & registers related to data processing
- consists of
  - functional units doing computations
  - data registers
  - wires & multiplexers connecting registers & functional units
- takes control signals as input, that define which computations are performed & where data is stored
- provides data values & derived data values (overflow, underflow, result of compares) as outputs
- visualized as block diagrams

Mapping to SysVerilog
- always_comb
  - describes functional units of data path
- always_ff
  - describes data registers

### Algorithmic State Machines

- useful extension to FSM
- allow to specify a system consisting of data path with control logic
- All FSM state diagrams have equivalent ASM diagram

The (<=) values only changes when changing state (A to B)

Value y gets old x value when
  x <= x + 1
  y <= x

---

## Processors

Von Neumann Model
- CPU
  - Processing Unit
    - ALU
    - Register File
  - Control Unit
    - Instruction Register
    - Program Counter
- Bus System
- Memory
- Input
- Output

Harvard Architecture
- Memory replaced with
- Data Memory
- Instruction Memory

### Arithmetic Logic Unit (ALU)

Combinational circuit performing calculation operations

Basic Properties
- takes 2 -bit inputs (A,B)
  - today typically 32 / 64 bit
- performs operation based on one / both inputs
  - performed operation is selected by control input alu_sel
- returns n-bit output
  - usually with additional status output with flags
    - indicating overflows
    - relations of A & B (eg. A==B, A < B)

### Register File

Essentially a memory with one write port & two read ports

- contains m n-bit registers
- in given clock cycle one n-bit value can be stored in register selected via signal R(W)
  - when RegWrite is low, no register is written
- in each cycle two registers can be read & are provided at outputs A & B
  - registers to be read are selected via R(A) & R(B)

Basic Properties
- data registers with two output MUX
- input stored in any of the registers
  - selection via R(W) signal
- typical register size 8 / 16 / 32 / 64 bit

### Instruction Register

Stores instruction that shall be executed by data path

Instruction decoder maps instruction register to control signals

#### Instruction Set Architecture (ISA)

- Instruction -> basic unit of processing on a computer
- Instruction set is set of all instructions on a given computer architecture
- ISA is interface between hardware & software
- Options to represent instructions
  - Machine Language
    - sequence of 0s & 1s (0x83200002)
      - sequence the processor takes to instruction register for decoding & execution
    - length varies, can be many bytes long
      - up to 15 bytes on x86 CPUs
  - Assembly Language
    - human readable representation of instruction
      - e.g. ADD x3, x1, x2

Many ISAs from different vendors
- Intel x86
- AMD64
- ARM
- MIPS
- RISC-V

Instruction sets vary significantly in terms of number of instructions
- Complex Instruction Set Computer (CISC)
  - not only load & store operations perform memory access, but also other instructions
  - design philosophy: many instructions, few instructions also for complex operations
  - hundreds of instructions that include instructions performing complex operations (like entire encryptions)
  - examples
    - x86
    - x64
- Reduced Instruction Set Computer (RISC)
  - load/store architectures
    - only dedicated load & store instructions read / write from / to memory
  - design philosophy: fewer instructions, lower complexity, high execution speed
  - instruction set includes just basic operations
  - examples
    - ARM
    - RISC-V
- One Instruction Set Computer (OISC)
  - computers with single instruction (academic)
  - example
    - SUBLEQ

Competition between Instruction Sets
- which instruction sets lead to
  - smallest code size
    - smallest number of instructions to express program
  - best performance on processor implementing ISA
  - lowest power consumption on processor implementing ISA

#### RISC-V Instruction Sets

Base instruction sets
- RV32I (used in course)
- RV64I
- RV128I

Extensions
- M -> standard extension for integer multiplication & division
- A -> standard extension for atomic instruction
- Zicsr -> Control & Status Register (CSR) instructions
- F -> standard extension for single-precision floating point

- R-Type Instructions
  - perform arithmetic & logic operations based on 2 input integers

Machine Language & Assembly
- every instruction can be represented in human readable form -> Assembly
- every instruction can be represented in machine readable form -> machine language
- strict 1:1 mapping

RV32I Instruction Set
- 40 instruction
- categories
  - integer computational instructions
    - all instructions take 2 input registers (rs1 & rs2) & compute result in rd
  - load & store instructions
  - control transfer instructions
  - memory ordering instructions
  - environment call & breakpoints

##### Integer Computational Instructions

All instructions take 2 input registers (rs1 & rs2) & compute result in rd

![Integer Computational Instructions](/res/int_comp_instr.png)

- Logic
  - AND
  - OR
  - XOR
- Arithmetic
  - ADD (addition)
  - SUB (substraction)
- Shifts
  - SLL (logical shift left)
  - SRL (logical shift right)
  - SRA (arithmetic shift right)
- Compares
  - SLT (set on less than)
  - SLTU (set on less than - unsigned)

### Memory

RAM -> Random Access Memory (arbitrary)

- Every memory has certain word size
  - for this lecture: 1 Word = 32 bit = 4 bytes
- Each byte in memory has address

Endianess
- two options for sequence of storing the bytes of a word in memory
  - little endian
    - least significant byte at lowest address
  - big endian
    - most significant byte at lowest address

Basic Idea of Memory Design
- Memories are built using memory cells, each one can store one bit
- Memory cells are placed on a chip next to each other & form rectangular strcuture (cell array)
- Bitline connects memory cells of column vertically
- Wordline connects memory cells of row horizontally
- Structure is used for all kinds of memories
  - Flash memory
  - Static memory
  - Dynamic memory
- Each type has a trade off (speed, size, ...)

Basic Idea of read/write for DRAM
- DRAM cell consists of single transistor & capacitance that stores data value
- steady states (no access) -> all bitlines & wordlines are disconnected from power supply
- Writing cell
  - set corresponding bitline to desired storage value
  - set corresponding wordline to high
  - -> charges capacitance of desired cell to desired storage value
- Reading cell
  - pre-charge corresponding bitline to desired voltage value
  - disconnect bitline
  - set corresponding wordline to high
  - -> bitline keeps value if storage is high or is pulled to low if stored value is zero

Example: Load Word
- Assembly
  - LW rd, offset(rs1)
- Load from data from memory at address (rm1+imm) & store in rd
- Functionality
  - Loads word (32 bits / 4 bytes) from memory into register
- Example
  - LW x5, 0x16(x1)

Example: ADDI
- Assembly
  - ADDI rd, rs1, immediate
- Functionality
  - Computes rd = rs1 + imm

More Operations with Immediates
- LUI
  - allows to load 20 bits into upper bits of register
  - allows, together with ADDI, to set register to constant 32 bit value
- SLTI
  - sets register rd to 1, if rs1 is less than sign-extended immediate
  - SLTIU is unisgned version
- XORI, ORI, ANDI
  - logic operations with immediates
- SLLI, SRLI, SRAI
  - shift operations, where 5 bit immediate "shamt" defines shift amount

### Instruction Memory

- stores sequence of instructions
- program counter (PC) is incremented by 4 in each cycle & reads one instruction after the other
- allows executing a static batch of instructions

Conditional Branches
- BNE
  - Branch if Not Equal
- BLT
  - Branch if Less Than
- BGE
  - Branch if Greater or Equal
- BLTU
  - Branch if Less Than Unsigned
- BGEU
  - Branch if Greater or Equal Unsigned

JAL / JALR
- Jump And Link
  - performs unconditional jump to PC + imm * 2
  - stores PC of next instruction in rd

### Interfacing with I/O Devices

Memory-mapped peripherals
- store & load instructions allow addressing 32-bit of memory space
- not all memory space that is addressable is used for actual memory
- memory space can be split in pieces & assigned a certain range to actual memory & other ranges to peripherals
  - load / store operations write to registers with additional functionality

Micro RISC-V CPU
- simple CPU
- implements large subset of R32I
- Overview
  - registers
    - zero register: x0
    - general purpose register: x1 - x31
  - memory
    - almost 2 KiB of memory (0x000 - 0x7fc)
    - memory-mapped I/O at address 0x7c

![Design Flow](/res/design_flow.png)

Pseudo Instructions
- to ease programming
- common instruction sequences
- instructions that can be derived from another instruction

---

## Programming a RISC-V CPU

Loop using label
  loop:
    ...
    BLT x3, x4, loop

JAL -> jump to specific label

### Function Calls

partitioning code into reusable functions  
return address needs to be stored

- JAL
  - Jump and Link
  - JAL rd, offset
  - jump destination is PC + offset
- JALR
  - Jump and Link Register
  - JALR rd, offset(s)
  - jump to address + offset

Nested subroutine calls
- need a stack
- stack
  - two operations
    - push
      - place element on stack
    - pop
      - receive element from stack
  - FILO (first in, last out) data structure
  - grows from high to low addresses
  - continuous section in memory
  - "stack pointer" (sp) "points" to "top of stack" (TOS)

Interacting with the stack
  ADDI x2, x0, x700 // create stack at x700
  ADDI x2, x2, -4   // add space to the stack
  SW x5, 0(x2)      // add value to place in stack

  LW x5, 0(x2)      // remove value from place in stack
  ADDI x2, x2, 4    // remove space on stack

#### Calling Convention

RISC-V Registers
- zero -> x0
- saved by caller
  - ra -> return address
  - a0-a1 -> argument/return values
  - a2-a7 -> arguments
  - t0-t6 -> temp. registers
- saved by callee
  - fp -> frame pointer
  - sp -> stack pointer
  - s1-s11 -> saved registers

Code Parts of Subroutine
- prolog (set up)
  - first instructions of subroutine
- neighborhood of nested call
  - before & after call
- epilog (clean up)
  - last instructions of subroutine

---

## Networking

### TCP/IP model

- Link layer
  - send chunk of data to directly connected computer
- Internet layer
  - route chunk of data to remote computer along series of direct links
- Transport layer
  - transmit structured bit stream across internet
- Application layer
  - offer services without having to worry about details

#### Link layer

Addressing
- address identifies destination
- MAC address
  - 48-bit identifier
  - locally unique
- Broadcast address
  - FF:FF:FF:FF:FF:FF
  - sent to all connected hosts

Checksum
- fixed size value
- calculated based on arbitrary data
- allows to detect errors
  - each message is sent with its correct checksum
  - if random bit gets flipped, checksum no longer correct
- does not help against intelligent attacker, just against "randomness"

##### Ethernet

Switches
- understand linked layer data
  - read source / destination MAC address
- record source addresses to build map address <-> port
- only forward packets to appropriate port
  - minimize wasted bandwidth
  - no collisions possible

Switching loops
- multiple switches can be interconnected to form one big network
- Problem
  - broadcast frames will multiply endlessly
- Spanning Tree Protocol (STP)
  - supported by professional switches
  - automatically disable redundant links until needed

Virtual-LAN
- partition switch ports into different logical networks
- devices on different networks cant send packages to each other
- broadcast packets are only broadcasted to the device's VLAN
- benefits
  - partitioned networks
  - no re-wiring required
  - configured in software
- downsides
  - configured in software

#### Network Layer

##### IPv4

- Internet Protocol, version 4
- foundation of today's internet
- 32-bit address
  - each participating network card has a single IPv4 address
- 32-bit subnet mask
  - all ones, followed by all zeros
  - splits address into network prefix & host number
  - alternate notations: specifying number of ones (/24)
  - dont need to be full bytes (/28)
- all hosts with same network prefix form a subnet
- hosts within same subnet can communicate directly
- 2 addresses per subnet have special meaning
  - host number all 0 -> network identifier
  - host number all 1 -> broadcast address
- private address space anyone can use
  - 10.0.0.0/8
  - 172.16.0.0/12
  - 192.168.0.0/16
  - -> not globally unique, won't work over internet
- configuring an IP address
  - ISP modem does this
  - DHCP (Dynamic Host Configuration Protocol)
  - Enabled by default on modern devices
- ARP (Address Resolution Protocol)
  - ethernet frames with type 0x8606
  - simple, stateless protocol
    - request MAC for given IP (ethernet broadcast)
    - target responds (ethernet unicast), now MAC address is known
  - heavily cached to avoid lots of broadcasting
  - destination address not in subnet
    - check routing table
    - maps destination address to next hop
      - move packets in right direction
    - send packet to next hop using linked layer
    - eventually gets there

IPv4 Packet overview
- version
  - always 0100
  - IPv4
- twin length fields
  - length of just the header
    - optional header extensions may make it longer
  - length of this packet
- safeguards
  - header checksum protects header integrity
    - guards against header corruption on lower layer
- time to live
  - limits how far a packet can travel
  - after 256 hops, packet is dropped
  - guards against routing issues
- fragmentation
  - happens if packet is too large for given connection
  - packet split into 2 or more packets
  - recipient re-assembles fragments
- identification is same across all fragments
- flags
  - whether this is not the last packet (More Fragments flag)
- fragment offset
  - fragment's position within original message

IPv4 fragmentation
- issues
  - 16-bit packet ID insufficient for high transmission rates
    - 16 bit packet ID -> 65536 pacekts in flight
    - no acknowledgements -> ID cant be reused until TTL expires
    - 512 packets per second
  - other issues
- alternatives
  - path MTU discover
    - detect largest packet size that can be sent unfragmented
  - how: complicated
    - Dont Fragment flag in IP header + trial & error
      - problem: failure notifications might not arrive
    - more sophisticated trial & error at higher layers
      - not every transport layer protocol can fragment data

##### IPv6

- Internet Protocol, version 6
- successor to IPv4
- Not natively interoperable with IPv4
  - IPv4-only devices can't communicate with IPv6-only devices
  - Most modern devices implement both IPv4 & IPv6
- 128-bit addresses
  - Notation: 16-bit hex blocks separated by colons (:)
  - zero blocks can be omitted by double colons (::)
- global addresses
  - valid in any network connected to internet
- unique local addresses
  - same as IPv4 private networks, no assignment needed
- link local addresses
  - only valid within Link Layer network
- IPv6 has no header checksum
  - relies on link layer to provide error detection
- fragmentation removed
- supported by most end-user devices
  - server side support still lacking

##### Network Address Translation

- Hide entire private network behind single public IP
- Transparent if client inside connects to server outside
  - reverse does not work
- NAT networks can be nested within NAT network
- Home ISP router does this

#### Transport Layer

- 2 protocols
  - TCP (Transmission Control Protocol)
    - focused on reliable delivery
    - connection based
  - UDP (User Datagram Protocol)
    - focused on speed
    - connectionless
- Ports
  - source & destination identified by port number
    - 16 bits (65536 available ports)
    - TCP & UDP ports separate
  - 2 applications cant use the same port number
  - port numbers standardized by IANA
    - 0-1023: well-known ports
    - 1024-49151: registered ports
      - most server apps use this range
    - 49152-65535: dynamic ports  
      - OS use this range for client ports

##### UDP

- useful for real time applications
- data loss must be tolerable for upper layer application
- simple & straight forward

##### TCP

- highly reliable transmission of byte stream
  - acknowledgements & retransmission
  - guaranteed to maintain data ordering
- non-trivial protocol overhead
- TCP connections have 2 sides
  - server
  - client
- server listens on specific port
  - fixed for all connections
- client connects to that port in server
- data ordering
  - Client -> Server: SYN
  - Server -> Client: SYN + ACK
  - Client -> Server: ACK
  - -> now both sides know that other side has their sequence number & are ready to communicate
- window size indicates how much more data the host can handle
- congestion control
  - each size throttles data transmission rate
  - concept
    - start at slow rate & increase speed until data gets lost
    - data gets lost -> slow down connection again
- Selective Acknowledgement
  - standard TCP does not deal with packet loss efficiently
  - SACK extension lets recipient acknowledge further ranges

- Flow Control protects recipient
  - recipient advertises capacity
  - sender abides to it
- Congestion Control protects network
  - transmission rate gradually increased
    - throttled back if packet loss detected
    - each side handles it independently

#### Application Layer

##### DNS

- Domain Name System
- Hierarchical structure
  - root nameservers (typically hardcoded)
  - at -> ask 127.30.48.1
  - tugraz.at -> ask 129.27.2.3
  - online.tugraz.at -> is at 129.27.2.210
- client queries DNS resolver on port 53
- DNS resolver performs actual recursive lookup if needed
  - allows centralized caching of responses
- DNS resolver address can be determined via DHCP
  - Dynamic Host Configuration Protocol
  - IP address auto-configuration

##### NTP

- Network Time Protocol
- time sync over internet
- used for many operations (MFA)
- UDP Port 123

##### SSH

- Secure Shell
- secure remote administration
- SSH popular as bulding block in other applications
  - provides auth & encryption
  - e.g.: git
- TCP port 22

##### BGP

- Border Gateway Protocol
- responsible for maintaining global IP routing table
  - distributed shortest-path graph algorithm
- TCP port 179

##### HTTP

- Hyper Text Transfer Protocol
- every web page uses it
- simple concept: ask server for document
- request
  - method
  - requested resource
  - request line
  - headers
- response
  - status code
  - status line
  - headers
- Methods
  - Safe methods
    - read only
    - GET
      - retrieve document
    - HEAD
      - retrieve response headers only
    - OPTIONS
      - retrieve acceptable request methods
  - Idempotent methods
    - safe to send multiple times
    - superset of safe methods
    - PUT
      - replace document
    - DELETE
      - delete document
  - Other methods
    - PATCH
      - make changes to existing document
    - POST
      - send generic data
- method functionality is purely by convention
  - nothing stopping you from deleting file via GET request
- client will offer different degrees of safeguards for different methods
  - reloading result of POST request triggers dialog box
- QUIC to replace TCP
  - Quick UDP Internet Connection
  - UDP at transport layer for minimal overhead
  - provide byte stream similar to TCP
- TCP port 80 (HTTP)
- TCP port 443 (HTTP over SSL)
  - SSL adds auth & encryption

---

## Memory Systems

- Speed of CPU & memory need to match
  - CPU is usually faster than memory
- Desired Memory Properties, only 2 of the 3 are possible
  - Fast
    - time between sending & receiving/writing should be short
    - amount of read/write data should be high
  - Large
    - many data words stored in memory
  - Cheap
    - low costs to build memory
- Properties of Memory Access
  - Temporal locality
    - data used recently likely to be used again
  - Spatial locality
    - data used recently likely to use nearby data
- Hierarchical Memory Design
  - combine all available memory types & pretend to be fast, large & cheap
  - store data in large memory
  - copy data that is used to small memory

### Cache

- generic term for structure that memorizes frequently used data
- instead of perorming slow operations repeatedly, store results of these operations
- managing caches
  - manual
    - programmer explicitly decides when which data is moved
  - automatic in software
    - software implements algorithm for automated caching
  - automatic in hardware
    - hardware moves data between memory levels transparently for software

#### Cache Design

- cache
  - receives address
  - checks if data for memory location is stored
  - yes -> data delivered
  - no -> data requested from next chache level & stored
- metadata stored in cache
  - cache also needs to store metadata
  - info on address of stored data blocks
  - bookkeeping data
- basic design principles
  - store & transfer larger blocks of memory between different levels of memory hierarchy
  - avoid searching entire cache upon access
- directly mapped caches
  - cache stores triplet (valid bit, tag, data) for each entry

#### Read Operation
    
- 3 bits of set field of address are used as index to read from cache memory
  - hit -> data delivered to CPU
- drawbacks
  - 2 blocks that map to same set can not be in cache at same time
  - can lead to low hit rates
  - introduction of "Set Associative Caches"
- Set Associative Caches
  - n-way SAC provides n storage locations for each set
  - each storage location is called "way"
  - upon access, HW searches all ways for cached data
- Fully Associative Cache
  - every address can be cached at every location
  - only done for small cache sizes
- changing block size
  - increase block size to improve hit ratio
- doubling cache size
  - double number of sets -> +1 bit in set selection
  - double block size -> +1 bit for block offset
  - double number of ways -> no change in address decoding

#### Replacement Policies

- miss -> new block needs to be stored in cache
- invalid blocks are used first
- all valid -> one block needs to be evicted
  - replacement policies
    - random
    - FIFO
    - least recently used
    - ...
  - implement LRU (Least Recently Used) policy
    - 2-way set associative cache
      - add metadata to indicate which way has been LRU
    - n-way set assoicative cache
      - implement LRU too expensive for 4+ way caches

#### Write Operations

- Write-Back Cache
  - data written to memory -> only update cache & not further up in memory hierarchy
  - write to next cache level when cache line evicted
  - pros
    - multiple writes in same block -> more efficient
  - cons
    - dirty bit to indicate whether block has been written to or not
    - more complex
- Write-Through Cache
  - update value in cache & next level of memory hierarchy
  - pros
    - simpler
  - cons
    - no combination of wires
    - more transfers between memory
- Allocate On Miss
  - transfer memory block into cache, if there is write on the block
  - pros
    - can combine wires
    - simpler design
  - cons
    - can lead to more memory transfers
- No Allocation on Miss
  - dont transfer block into main memory on write
  - pros
    - use less cache space
  - cons
    - no combination of wires

#### Which Cache to use where?

- options for cache organization
  - cache size
  - associativity
  - block size
- additional options for implementation
  - replacement policy
  - write handling
  - separate cache for instructions & data vs unified cache
- goal of overall memory system is to minimize Average Memory Access Time (AMAT)
  - minimize cache latency & number of cache misses at each level
- reasons for cache misses
  - compulsory misses
    - cache misses that occur independent of cache design
  - capacity miss
    - cache misses that occur because the cache cant store all needed data concurrently
  - conflict misses
    - cache misses that occur because different addresses map to same set & evict blocks still needed
- effect of parameters
  - increasing block size can reduce compulsory misses, but increase conflict misses
  - increasing capacity can decrease capacity & conflict misses, but not compulsory misses

### Virtual Memory

- make memory appear as almost infinite resource
- additional level of abstraction
- Properties
  - not working with physical addresses
  - each process has own mapping of virtual to physical addresses
  - HDD is added as additional memory
- Benefits
  - relocation
  - sharing of memory between processes
  - isolation of processes
- address translation is done at level of pages
  - virtual memory divided in virtual pages
  - virtual pages mapped to physical pages (frames) via page table
- main memory acts as fully associative cache for HDD (managed by OS)
  - every virtual page can map to every physical page
  - memory access to virtual page not mapped to physical page leads to page fault
  - access outside address space of process -> segmentation fault
- analogies for caches <-> virtual memory
  - block <-> page
  - block size <-> page size
  - block offset <-> page offset
  - miss <-> page fault
  - tag <-> virtual page number
- page table
  - maps virtual to phyiscal addresses
  - indexed via virtual page number
  - contains valid bit & physical address for every Page Table Entry (PTE)
  - contains additional metadata
  - typical page table entry size on 32-bit systems -> 1 word (4 byte)
  - storing page table
    - large
    - stored in physical memory
    - page table base register specifies location of page table in physical memory
- Memory Access (Load/Store)
  - 1. CPU sends virtual address to memory management unit (MMU)
  - 2. MMU determines PPN by performing load from PTBR + VPN * PTE_SIZE
  - 3. entry not valid -> swap page from HDD to main memory
  - 4. entry valid -> concat offset of virtual address to PPN to receive physical address of data
  - 5. perform load / store based on physical address
- Translation Lookaside Buffer (TLB)
  - cache for page-table entries
  - highly / fully associative
  - typically 16-512 entries
  - TLB achieves high hit rates because of high spatial / temporal locality
- multi-level page tables
  - hierarchical page-table scheme only requires to keep smaller first-level page table in physical memory
  - 32-bit systems -> 2-level page tables (up to 5 levels on 64-bit)
- provides fresh empty memory for each process
- increases memory capcity by adding HDD as additional level of hierarchy, OS manages swapping
- mechanisms
  - MMU maps virtual addresses to phyiscal addresses via page tables
  - page table entries contain metadata
  - TLB acts as cache for page tale entries

### Combining Virtual Memory & Caches

- optimizing speed - positioning of cache
  - PIPT
    - Physically Indexed Physically Tagged Cache
    - CPU -> TLB -> Cache -> Next Cache / Memory
    - wait with cache access until TLB delivers physical address
  - VIVT
    - Virtually Indexed Virtually Tagged Cache
    - CPU -> Cache -> TLB -> Next Cache / Memory
    - cache works on virtual address only, no waiting for TLB
    - TLB access only needed on cache miss
    - different processes -> different mappings (virtual tag not unique) -> shared memory can be in cache multiple times
  - VIPT
    - Virtually Indexed Physically Tagged Cache
    - CPU -> TLB / Cache -> Next Cache / Memory
    - virtual address used to index cache, physical address used for tagging of cache
    - advantage -> lookups of TLB & cache can be started in parallel
    - disadvantage -> aliasing may occur if process accesses same physical memory via two different virtual addresses
    - typical compromise
      - keep size of cache low, so tagging is done using address bit of page offset (same for virtual & physical address)

### Processor Design

- goal of processor design is maximizing executed number of instructions per time
- determined by 2 factors
  - needed clock Cycles Per Instruction (CPI)
  - clock frequency, which determines number of cycles per second
- multicycle architectures
  - cut operations needed for one instruction into more fine-granular operations
  - each instruction is multicycle instruction & takes as many cycles as needed to perform actions defined by instruction
    - instructions lead to different number of operations
    - operations done in a clock cycle are less complex & frequency can be increased
  - Fetch
    - given address, read instruction from memory
  - Decode
    - given instruction, determine corresponding control signals for data path
  - Execute
    - given control signals & data inputs, perfurm necessary ALU operations
  - Memory
    - if needed, send address to memory to receive data
  - Write Back
    - write result of instruction back to register file

### Pipelining

- divide instruction processing into different stages
- dont complete execution of one instruction before starting execution of next instruction
- process different instruction in each stage
- ideal setup for pipelining
  - identical operations
  - independent operations
  - uniform partitioning into suboperations possible
- reality -> no point of the ideal setup is certain
  - different instructions
  - dependencies between instructions, which need to be resolved to compute correct result
  - different latencies on different pipeline stages
- 20 pipelines stages on average in normal CPU
- pipeline stall
  - condition that prevents pipeline from moving
  - necessary when a resource / data is not available on a pipeline stage

#### Data Dependence Handling

- Read-After-Write dependency (RAW)
  - instruction tries to access register that has not yet been written back to register file -> data hazard
- Write-After-Read dependency (WAR)
  - instruction tries to access register that is needed for input of other instruction
- Write-After-Write (WAW)
  - instruction writes to register that is also written to by another instruction
- dependence detection
  - scoreboarding
    - associate valid bit with each register of register file
    - when instruction writing to register is decoded, reset valid bit
    - instruction in decode stage checks if all source registers are valid
- dependence handling after detection
  - stall pipeline
  - stall pipeline only when necessary -> data forwarding/bypassing
    - idea: data value already available in later pipeline stage -> get it from there instead of register file
    - benefit: consumer can move pipeline until point value can be supplied -> less stalling
- data forwarding
  - check if source register of instruction transitioning to execute stage matches destination register of instruction in memory / writeback stage
  - if so, forward result -> data values supplied to dependent instruction as soon as available

#### Control Hazards

- keep pipeline full, fetch instruction every cycle -> know what instruction to fetch next
- linear code sequences -> next instruction located at next memory address
- control-flow isntructions occur -> 2 possible execution paths (branch taken - branch not taken) causing control hazard
- control hazard occurs, when decision of what instruction to fetch is not available by time fetching occurs
- Early Branch Resolution reduces performance penalty of branches
- Branch Prediction -> instead of waiting, try to predict whether branch is taken or not
- Dynamic Branch Prediction
  - hardware learns software behavior & predicts based on past behavior
  - simplest predictor -> store for given branch if taken or not during last execution
- Branch Target Buffer
  - store information about the branch history to predict behavior
  - can not create a table for all branches of potential addresses
  - mapping function based on lower bits of the address used to index table about branching history
- Superscalar Design
  - more pipeline stages -> shorter critical path per stage -> higher clock frequency
  - instead of processing one instruction per stage, process multiple in each stage
  - issue multiple Instructions Per Cycle (IPC)
- Multiple-Issue Processors -> handle multiple instruction in each clock cycle
- Out-of-Order Execution
  - execute instructions out of order & store results in temporary registers
  - perform writeback from temporary registers to register file in order
  - higher throughput due to better hardware utilization
  - execute instruction whenever it is ready to execute
  - idea
    - instructions fetch & decode
    - dependencies resolved
    - execute ready instruction wait for execution in reservation station -> OoO execution
    - results provided to commi tunit forwarding results back to register file in order

#### Register Renaming

- resolve WAR & WAW dependencies by adding registers in microarchitecture
- programmer doesn't see registers & can't access them
- hardware has larger pool of registers & needs to know which register corresponds to to the software
- Reorder Buffer (ROB)
  - hardware unit storing additional register components
  - working principle
    - instruction decoded -> reserve entry in record buffer
    - after instruction completion -> reorder buffer stores result of instruction
    - oldest instruction in buffer completed -> result of instruction is moved to register file
  - stores information about all decoded instructions not yet retired / commited
  - valid bit to track which instructions have completed executions
  - needs to store additional meta data
    - correctly reorder instructions back to to program order
    - update architectural state with instruction result upon retiring
    - handle exceptions / interrupts
- reading inputs from register buffer
  - read input registers for instructions from register file / directly from reorder buffer

---

## Interrupts & Multitasking

- basic protocol: interface with control register
  - sender waits until valid bit is cleared
  - sender sets data value
  - sender sets valid bit
  - receiver (software) waits until valid bit is set
  - receiver reads data
  - receiver clears valid bit

### Communcation via a Slow Interface

- polling is highly inefficient - cpu is stuck in a loop until
  - I/O peripheral sets ready signal
  - timer has reached certain value
  - user has pressed key
  - ...
- alternative
  - CPU keeps executing some useful code in first place
  - use concept of interrupts to react to "unexpected" events
  - idea: instead of waiting for event, execute useful code & let event trigger a redirection of instruction stream

### Handling unexpected external events

- add input signal to CPU called "interrupt"
- external signal can activate interrupt
- after executing instruction, CPU checks value of interrupt signal before it fetches next instruction
- if interrupt active, next instruction to be executed is first instruction of "interrupt-service routine"
- after handling interrupt, CPU returns to interrupted program

#### Interrupts in RISC-V

- hardware aspects
  - external interrupt is input signal to processor core
  - Control & Status Registers (CSRs) for interrupt configuration
  - additional instructions for interrupt handling
  - dedicated interrupt controllers on bigger porcessors
- software aspects
  - when interrupt occurs, program execution is interrupted
  - functions have to handle interrupts -> Interrupt Service Routines (ISR)
  - software needs to configure & enable interrupts
  - software has to preserve interrupted context
    - interrupt entry points typically written in assembly

##### ISR

- Interrupt Service Routine
- entering ISR
  - upon interrupt, processor
    - jumps to location in memory specified by mtvec CSR
    - automatically stores previous location into mepc CSR
- executing ISR
  - ISR can execute arbitrary code
  - processor context needs to have exact same values when returning to interrupted codes
- leaving ISR
  - upon execution of mret isntruction, processor returns to original location stored in mepc CSR
- finding ISR
  - 2 approaches
    - single entry point for all interrupts
      - ISR has to determine what caused interrupt & handle corresponding interrupt
    - multiple entrypoints for different interrupts organized in a table (vectored interrupts)
      - table defines entry point for different causes of interrupts
  - RISC-V allows both approaches

### DMA

- Direct Memory Access
- when CPU receives data from peripheral, needs to do 2 things
  - CPU first copies data from peripheral to memory
  - CPU then works with data in memory to perform specific tasks
- simple task, sequence of load & store operations
- can take long for large data blocks -> loop that continously loads data from peripheral to memory
- add DMA to system that interacts with peripherals & copies data between memory & peripherals
  - CPU can do more useful stuff
- idea
  - offload memory transfer task from CPU to DMA
- implementation
  - CPU configures DMA control with basic parameters
  - after completion of transfer, DMA triggers input

### Multitasking

- resource manager takes care of 
  - which program executed when using what resources
  - context switching (switching from one program to another)
  - managing sharing all kinds of resources (CPU, memory, I/O, ...) between program 
- done by OS
- privilege levels provided by hardware
  - not every program should be able to do everything, OS should have different rights
  - computers have typically at least 2 modes
    - user mode
      - mode for user applications providing limited access to HW resources
    - supervisor mode
      - mode for OS providing full access
  - RISC-V provices 3 modes
    - user mode (U)
    - supervisor mode (S)
    - machine mode (M)
  - switching between privilege levels done by software exceptions
- HW provides three options to execute multiple programs on computer
  - execute multiple programs on single CPU
  - execute on multiple CPUs
  - simultaneous multithreading
- multiple programs on single CPU
  - cooperative multitasking
    - programs that execute on CPU yield control of CPU to each other
  - preemptive multitasking (common approach)
    - OS decides which tasks run on CPU
    - task requires timer interrupt
- timer interrupt
  - all systems implement timer peripheral, allows to receive interrupt after given time
- preemptive multitasking (time slicing)
  - OS maintains list of tasks to be executed
  - schedules first task & sets timer interrupt
  - first task runs for given time & then interrupted

#### Multiprocessor Design

  - 2 types
    - symmetric multiprocessors
      - same core simply instantiated multiple times
    - heterogenous multiprocessors
      - system has cores with different properties
  - basic design
    - multiple CPU cores
    - all connected to shared memory
  - datacenters (e.g. AWS) have dedicated designs

#### Hardware Multithreading

- superscalar CPU design -> single thread will not need all HW resources on different pipeline stages
- idea
  - instead of just executing one thread in CPU, execute multiple in parallel
  - no dependencies between instructions of different threads -> should not increase throughput
- simultaneous multithreading
  - coarse grained
    - switch between threads upon major stalls
  - fine grained
    - switch between threads in every clock cycle
  - simultaneous multithreading
    - permanently fetch, decode & execute instructions from different threads
