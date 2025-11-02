Synchronous FIFO (First-In, First-Out)

This repository contains the Verilog implementation of an 8-bit wide, 16-deep synchronous FIFO (First-In, First-Out) buffer, along with a reusable SystemVerilog verification environment (Testbench).

The verification environment utilizes a class-based structure, including a Generator, Driver, Monitor, and Scoreboard, to implement robust, self-checking randomized testing.

1. Project Structure

File

Description

Language

FIFO.v

Design Under Test (DUT). Contains the synchronous FIFO module logic.

Verilog

fifo_tb.sv

Verification Environment. Contains the SystemVerilog interface, transaction class, and all UVM-like verification components.

SystemVerilog

README.md

This file.

Markdown

2. Design Under Test (FIFO.v)

The FIFO is a Synchronous FIFO, meaning read and write operations are controlled by the same clock signal (clk).

Specifications

Data Width: 8 bits (din, dout).

Depth: 16 entries (Memory size mem [15:0]).

FIFO Type: Synchronous.

Implementation: Uses dedicated read/write pointers (rptr, wptr) and a counter (cnt) to manage the fill level and determine the empty and full status.

Module Interface

module FIFO(
    input clk, rst, wr, rd,
    input [7:0] din, 
    output reg [7:0] dout,
    output empty, full
);



3. Verification Environment (fifo_tb.sv)

The testbench is built using SystemVerilog classes to promote reusability and structured testing, following the standard methodology architecture.

3.1. Verification Components

Component

Role

Details

fifo_if

Interface

Defines the connection ports between the testbench and the DUT, providing clocking and synchronization logic.

transaction

Data Packet

A class holding randomized control signals (oper, data_in) and observational data (data_out, full, empty). Includes a constraint for 50/50 read/write operation selection.

generator

Stimulus Creator

Randomizes transaction objects and sends them to the Driver via a mailbox. Paces transaction generation using an event signaled by the Scoreboard.

driver

Interface Agent

Receives transactions from the Generator and translates them into pin-wiggles on the virtual interface (fif) connected to the DUT.

monitor

Observational Agent

Sits passively on the interface, samples the DUT's input/output ports at the appropriate clock edges, packages the observations into a transaction object, and sends it to the Scoreboard.

scoreboard

Checker (Reference Model)

Receives monitored transactions. It uses a SystemVerilog queue (din[$]) as the reference model to mimic ideal FIFO behavior (push_front, pop_back). It compares the DUT's data_out against the expected value from the queue and reports mismatches.

environment

Top-Level Controller

Instantiates all verification components, connects the mailboxes, manages the test sequence (reset, run, post-test summary), and sets the transaction count.

3.2. Reference Model Logic

The scoreboard implements a behavioral model of the FIFO using a queue:

Write Operation (Push): If wr is asserted and full is de-asserted, the input data is added to the front of the queue: din.push_front(tr.data_in).

Read Operation (Pop): If rd is asserted and empty is de-asserted, the expected data is removed from the back of the queue: expected_data = din.pop_back(). This expected_data is then compared against the DUT's tr.data_out.

4. Simulation Instructions

To simulate this design, you can use any standard SystemVerilog simulator (e.g., Icarus Verilog, VCS, QuestaSim).

Using Icarus Verilog (Iverilog)

If you are using Icarus Verilog (a common open-source option), you can run the simulation using the following commands:

Compile the design and testbench:

iverilog -g2012 FIFO.v fifo_tb.sv -o fifo_sim


Note: The -g2012 flag enables SystemVerilog features.

Run the simulation:

./fifo_sim


View Waveforms (Optional):
The testbench automatically generates a dump.vcd file. Use a waveform viewer like GTKWave to inspect signals:

gtkwave dump.vcd


The simulation will execute 25 randomized transactions and provide a summary of any data mismatches detected by the Scoreboard.
