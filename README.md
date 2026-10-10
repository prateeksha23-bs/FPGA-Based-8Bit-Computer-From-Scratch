# 🖥️ FPGA-Based 8-Bit Computer — Designed from Scratch

An 8-bit accumulator-based computer implemented in **Verilog HDL** and deployed on a **Spartan-7 FPGA** using Xilinx Vivado.

This project explores the fundamental concepts of computer architecture and digital system design by building a custom processor with an instruction set, hardwired control unit, arithmetic logic unit, registers, RAM, and physical hardware debugging capabilities.

The processor executes instructions through a **Fetch–Decode–Execute cycle**, demonstrating how individual hardware components communicate to form a working computer.

---

## 🚀 Project Overview

The objective of this project is to design, simulate, and implement an 8-bit computer from scratch using Verilog HDL.

The processor features a custom 16-instruction instruction set, an accumulator-based datapath, a hardwired finite-state-machine (FSM) control unit, a 16 × 8 RAM, arithmetic and logical operations, conditional branching, and output interfacing.

The design is implemented on a Spartan-7 FPGA board, with physical LEDs and a seven-segment display used to observe the processor's behavior.

### ✨ Key Features

- 🧠 Custom 8-bit accumulator-based CPU architecture
- ⚙️ Hardwired FSM control unit
- 📋 Custom 16-opcode instruction set
- ➕ Arithmetic operations: ADD and SUB
- 🔢 Logical operations: AND, OR, XOR, and NOT
- ↔️ Bit-shift operations
- 🏁 Unconditional and conditional branching
- 💾 16 × 8 RAM for instructions and data
- 🚦 Zero-flag-based conditional branching
- 📥 External input and output instructions
- 🛑 HALT instruction and status indication
- 🔍 Physical-switch-based CPU debug selection
- 💡 Eight-LED output and hexadecimal seven-segment display
- 🧪 Verilog simulation and FPGA hardware testing
- 🛠️ Vivado synthesis, implementation, and bitstream generation

---

## 🏗️ System Architecture

The processor consists of interconnected datapath and control modules.

### Main Components

| Component | Function |
|---|---|
| Program Counter (PC) | Holds the address used for the next instruction fetch |
| Memory Address Register (MAR) | Holds the address selected for a memory access |
| Instruction Register (IR) | Stores the current 8-bit instruction |
| RAM | Stores 16 words of 8-bit instructions and data |
| Accumulator (ACC) | Stores the primary operand and computational results |
| B Register | Holds the second operand for ALU operations |
| Arithmetic Logic Unit (ALU) | Performs arithmetic, logical, and shift operations |
| Flag Register | Stores status flags, including the zero flag |
| Control Unit | Generates control signals using a hardwired FSM |
| Output Register | Holds the value presented by the OUT instruction |
| Clock Divider | Generates a slower CPU clock from the board clock |

### 🔄 Processor Data Flow

1. The Program Counter provides the instruction address.
2. The Memory Address Register selects the address for RAM access.
3. RAM supplies the instruction to the Instruction Register.
4. The Control Unit decodes the opcode.
5. The FSM generates control signals for the required operation.
6. The datapath performs register transfers, memory access, or ALU operations.
7. The result is stored in the appropriate register.
8. The OUT instruction transfers the accumulator value to the output register.
9. Execution continues until the CPU encounters a HALT instruction.

---

## ⚙️ Control Unit Design

The processor uses a **hardwired finite-state-machine control unit** to coordinate the execution of instructions.

Instead of using a microprogrammed control memory, the controller uses explicitly defined states and transition logic to generate the control signals.

### Main FSM Phases

- **Fetch:** Selects the instruction address and retrieves the instruction from RAM.
- **Decode:** Examines the opcode in `IR[7:4]`.
- **Execute:** Performs the operation associated with the decoded instruction.
- **Halt:** Stops normal instruction execution when HLT is encountered.

The FSM includes states such as `FETCH_0`, `FETCH_1`, `FETCH_2`, and `DECODE`, along with instruction-specific execution states.

### Important Control Signals

| Signal | Purpose |
|---|---|
| `pc_inc` | Increments the Program Counter |
| `pc_ld` | Enables loading a new PC value |
| `mar_ld` | Loads the MAR |
| `ir_ld` | Loads the Instruction Register |
| `ram_rd` | Enables RAM read data |
| `ram_wrt` | Enables RAM write operations |
| `acc_ld` | Enables accumulator loading |
| `b_ld` | Loads the B Register |
| `alu_op` | Selects the ALU operation |
| `acc_src` | Selects the accumulator data source |
| `out_ld` | Loads the output register |
| `flag_we` | Enables flag-register updates |
| `hlt` | Indicates the halted state |

---

## 📋 Instruction Set Architecture (ISA)

Each instruction is 8 bits wide.

| Bits | Description |
|---|---|
| `IR[7:4]` | 4-bit opcode |
| `IR[3:0]` | 4-bit immediate, operand, or address field, depending on the instruction |

### Complete Instruction Set

| Opcode | Mnemonic | Description |
|---|---|---|
| `0000` | NOP | No operation |
| `0001` | LDA | Load accumulator from RAM |
| `0010` | STA | Store accumulator to RAM |
| `0011` | ADD | Add RAM data to accumulator |
| `0100` | SUB | Subtract RAM data from accumulator |
| `0101` | JMP | Unconditional jump |
| `0110` | LDI | Load an immediate value |
| `0111` | INP | Load external input into the accumulator |
| `1000` | OUT | Transfer accumulator data to the output register |
| `1001` | HLT | Halt normal execution |
| `1010` | AND | Bitwise AND |
| `1011` | OR | Bitwise OR |
| `1100` | XOR | Bitwise XOR |
| `1101` | NOT | Bitwise inversion |
| `1110` | SHIFT | Shift left or right |
| `1111` | JZ | Jump if the zero flag is set |

---

## ➕ Arithmetic and Logic Unit

The ALU operates on 8-bit data and supports arithmetic, bitwise logic, and shift operations.

### Supported Operations

- Addition
- Subtraction
- Bitwise AND
- Bitwise OR
- Bitwise XOR
- Bitwise NOT
- Logical shift left
- Logical shift right

The ALU receives operands from the accumulator and B Register. Its result can be written back to the accumulator under control of the FSM.

### Flag Register

The flag register contains zero and carry status outputs.

- **Zero flag:** Records whether the ALU result is zero when the flag update is enabled. The JZ instruction uses the registered zero flag to make a conditional branch.
- **Carry flag:** Present in the current design, but carry detection is not fully implemented; the carry input is tied to logic zero.

---

## 🔍 Hardware Debugging and Monitoring

The processor includes physical debugging features to make internal CPU behavior observable during FPGA operation.

### Slide-Switch Debug Selector

Two physical slide switches, `SW1` and `SW0`, select the signal displayed on the eight LEDs.

| SW1 | SW0 | Selected signal |
|---|---|---|
| `0` | `0` | CPU output |
| `0` | `1` | Program Counter |
| `1` | `0` | Accumulator |
| `1` | `1` | Instruction Register |

The selected debug value is displayed on the eight LEDs without changing the CPU's instruction execution.

### Seven-Segment Display

The seven-segment display shows the actual CPU output in hexadecimal, independently of the selected LED debug view.

For example, a CPU result of decimal 8 is displayed as `08`.

### HALT Indicator

The RGB0 green indicator is driven by the CPU's halt signal to indicate that the processor has reached its halted state.

### Virtual Input/Output (VIO)

The Vivado VIO core provides hardware-debug controls for RAM loading and external input, and monitors selected CPU status and output signals.

---

## 🧪 Demonstration Program

A basic demonstration program validates instruction fetching, immediate loading, RAM access, arithmetic execution, output, and halt behavior.

### Program

| Address | Binary Instruction | Meaning |
|---|---|---|
| `0` | `01100101` | `LDI 5` |
| `1` | `00111000` | `ADD 8` |
| `2` | `10000000` | `OUT` |
| `3` | `10010000` | `HLT` |
| `8` | `00000011` | Data value 3 |

### Execution

1. `LDI 5` loads 5 into the accumulator.
2. `ADD 8` adds the value stored at RAM address 8, which is 3.
3. The accumulator receives the result 8.
4. `OUT` transfers the result to the output register.
5. `HLT` halts the processor.

**Expected result:**

\[
5+3=8
\]

The eight LEDs and seven-segment display show the output value `08` when the CPU output debug mode is selected.

### Fibonacci Sequence Demonstration

A separate demonstration program loads predefined Fibonacci values into the accumulator and displays them sequentially:

`0, 1, 1, 2, 3, 5, 8`

This demonstrates sequential instruction execution and output updates. The current version uses immediate constants rather than computing each Fibonacci term from the previous two terms.

---

## 🛠️ Hardware and Development Tools

| Item | Details |
|---|---|
| HDL | Verilog |
| FPGA board | Real Digital Boolean Board |
| FPGA family | AMD/Xilinx Spartan-7 |
| FPGA part | `XC7S50-CSGA324-1` |
| Development environment | Xilinx Vivado |
| Memory initialization | `$readmemb` with `program.mem` |
| Hardware outputs | Eight LEDs, seven-segment display, RGB status indicator |
| Debug interface | Physical slide switches and Vivado VIO |

---

## 📁 Project Structure

The project is organized into separate Verilog modules for modular design and testing.

```text
FPGA-8-Bit-Computer/
├── README.md
├── program.mem
├── cpu_top.v
├── cpu_core.v
├── control_unit.v
├── alu.v
├── accumulator.v
├── b_register.v
├── program_counter.v
├── mar.v
├── inst_reg.v
├── ram.v
├── flag_register.v
├── output_reg.v
├── clk_divider.v
├── sevenseg_output.v
├── constraints/
│   └── cpu_top.xdc
└── testbenches/
    ├── tb_cpu_top.v
    ├── tb_ram.v
    ├── tb_program_counter.v
    ├── tb_mar.v
    └── tb_timer_peripheral.v
```

*The tree represents the intended organization. Keep only files and testbenches that actually exist in the repository.*

---

## 🚀 Implementation Workflow

1. Design the CPU modules in Verilog HDL.
2. Develop and verify the control unit and datapath.
3. Create testbenches for individual modules and the integrated design.
4. Initialize RAM using `program.mem`.
5. Add the Boolean board's XDC constraints.
6. Run RTL elaboration in Vivado.
7. Run synthesis and implementation.
8. Generate the FPGA bitstream.
9. Program the Spartan-7 board.
10. Verify CPU output, debug selection, seven-segment display, and HALT indication on hardware.

---

## 🎯 Learning Outcomes

This project provides practical experience with:

- Computer architecture and instruction-set design
- RTL design using Verilog HDL
- Finite-state machines and hardwired control
- Datapath construction and register transfers
- ALU design and flag-based conditional branching
- RAM organization and instruction encoding
- Simulation, synthesis, and FPGA implementation
- Hardware debugging using physical switches and displays
- Integration of multiple digital modules into a functioning processor

---

## 🔮 Future Improvements

Potential improvements include:

- A physical START pushbutton to control program execution
- A true Fibonacci implementation using arithmetic and branching
- Full carry and borrow flag generation
- Additional general-purpose registers
- Improved instruction-level debugging and execution tracing
- Memory and register visualization during execution
- Additional instructions and expanded processor functionality

---

## 👨‍💻 Conclusion

Building an 8-bit computer from scratch provides a practical understanding of how processors fetch instructions, decode opcodes, execute operations, and coordinate data movement between registers, memory, and the ALU.

By implementing the design in Verilog and deploying it on a Spartan-7 FPGA, this project bridges the gap between computer architecture theory and real digital hardware.

**Designed in Verilog. Implemented on FPGA. Built to understand the computer from the inside out.** ⚡


---

## 📌 Disclaimer

This project is developed **strictly for educational and learning purposes only**. It is intended to demonstrate fundamental concepts of computer architecture, digital logic design, Verilog HDL, and FPGA implementation. It is not intended for commercial use or deployment in safety-critical applications.

The project is provided as an educational resource to encourage experimentation, learning, and further development.
