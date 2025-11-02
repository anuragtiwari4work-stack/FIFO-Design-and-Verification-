# Synchronous FIFO (First-In, First-Out)

This repository contains the Verilog implementation of an 8-bit wide, 16-deep synchronous FIFO (First-In, First-Out) buffer, along with a reusable SystemVerilog verification environment (Testbench).

The verification environment utilizes a class-based structure, including a Generator, Driver, Monitor, and Scoreboard, to implement robust, self-checking randomized testing.

## 1. Project Structure

| File | Description | Language | 
| :--- | :--- | :--- | 
| `FIFO.v` | **Design Under Test (DUT).** Contains the synchronous FIFO module logic. | Verilog | 
| `fifo_tb.sv` | **Verification Environment.** Contains the SystemVerilog interface, transaction class, and all UVM-like verification components. | SystemVerilog | 
| `README.md` | This file. | Markdown | 

## 2. Design Under Test (`FIFO.v`)

The FIFO is a **Synchronous FIFO**, meaning read and write operations are controlled by the **same clock signal (`clk`)**.

### Specifications

* **Data Width:** 8 bits (`din`, `dout`).

* **Depth:** 16 entries (Memory size `mem [15:0]`).

* **FIFO Type:** Synchronous.

* **Implementation:** Uses dedicated read/write pointers (`rptr`, `wptr`) and a **counter (`cnt`)** to manage the fill level and determine the `empty` and `full` status.

### Module Interface

```verilog
module FIFO(
    input clk, rst, wr, rd,
    input [7:0] din, 
    output reg [7:0] dout,
    output empty, full
);
