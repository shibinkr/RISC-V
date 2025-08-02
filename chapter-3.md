# Chapter 3: The RISC-V Assembly Workbook

## 3.1 Features of RISC-V Assembly Language

The RISC-V assembly language reflects the design goals of the RISC-V Instruction Set Architecture (ISA). It emphasizes simplicity, consistency, and direct control over the hardware. This chapter outlines key features that make RISC-V assembly both practical and educational.

---

#### 1. Designed with Simplicity in Mind

RISC-V was created to be **simple and minimal**, avoiding unnecessary complexity. Every instruction follows a clean format, and the instruction set contains only what is necessary to write efficient programs.

- **Benefit**: Easier to learn and implement.
- **Example**: All base instructions are 32 bits wide, simplifying decoding and hardware design.

---

#### 2. Three-Operand Format for Binary Operations

Most arithmetic and logical operations follow a **three-operand format**:

```assembly
add x3, x1, x2   # x3 = x1 + x2
```
- **Two source registers**: `x1`, `x2`
- **One destination register**: `x3`

This consistent pattern simplifies both coding and CPU instruction decoding.

- **Benefit**: Reduces confusion and supports cleaner, more efficient hardware pipelines.

---

#### 3. Support for Pseudoinstructions

RISC-V includes **pseudoinstructions** to make assembly code more readable and concise. These are not actual instructions in the hardware but are automatically translated by the assembler into valid machine instructions.

- **Example**:
  ```assembly
  li x5, 100   # Load immediate value 100 into register x5
  ```

- **Translates to**:
  ```assembly
  addi x5, x0, 100

- **Why this matters**: Pseudoinstructions make the assembly code easier to write and read, especially for common tasks. They’re like syntactic sugar that simplifies development without changing the underlying machine code behavior.

---

#### 4. Direct Translation to Machine Language

Each RISC-V assembly instruction has a one-to-one (or in some cases, one-to-few for pseudoinstructions) mapping to machine code.

- **Example**:
  ```assembly
  add x3, x1, x2    →    0x002081B3	
  ```
**Why this matters**: This direct translation allows for:

- Efficient assemblers and disassemblers  
- Predictable performance  
- Easy debugging and reverse engineering  
- Reliable educational tools for learning how CPUs execute instructions  

---

#### Summary Table

| **Feature**           | **Explanation**                                                              |
|------------------------|------------------------------------------------------------------------------|
| Simplicity             | Easy to learn, implement, and scale                                          |
| 3-Operand Format       | Uniformity in instruction format improves readability and hardware design   |
| Pseudoinstructions     | Makes coding easier without adding complexity to hardware                   |
| Direct Translation     | Ensures close mapping to machine instructions, aiding performance and clarity |

---

### 3.1.1 How the RISC-V Assembly Language Works

The syntax of the RISC-V assembly language can change depending on two main things:

- The variant or extension of the RISC-V architecture you're targeting (since RISC-V is modular and supports different instruction sets)
- The specific assembler tool you use (the program that reads your assembly code text file and converts it into machine code the CPU can run)

---

#### RISC-V Assembly Language Elements

To write RISC-V assembly programs, you use several core elements:

1. **Instructions**
    - These are the basic operations executed by the processor.
    - They include arithmetic (add, subtract), load/store (reading and writing memory), and control flow (branches, jumps).
    - Each instruction has a mnemonic (a short, descriptive name like `add` or `lw`) and one or more operands (registers or data it operates on).

2. **Registers**
    - Small, fast storage locations inside the CPU.
    - Used to hold temporary data and intermediate results during computation.

3. **Labels**
    - Symbolic names representing memory addresses.
    - Used as targets for branch or jump instructions, making code easier to read and write.

4. **Directives**
    - Special commands that guide the assembler on how to process the code.
    - For example, directives might set where in memory the code or data should go, or define constant values.

5. **Macros**
    - User-defined sequences of assembly instructions.
    - Allow you to reuse a set of instructions with a single macro call, improving code modularity and readability.

6. **Pseudoinstructions**
    - Not real hardware instructions but synthetic instructions that the assembler translates into one or more actual machine instructions.
    - They provide a simpler, higher-level way to write assembly code without sacrificing performance.

---

### 3.1.2 Assembler Directives in RISC-V

Assembler directives are special commands used in assembly language programming. They're **not instructions for the CPU**—meaning they aren't translated into machine code to be executed—but instead provide instructions to the **assembler** (the program that turns assembly code into machine code).

Think of them as _"behind-the-scenes"_ commands that help organize and manage your program during the assembly process.

---

####  What Do Assembler Directives Do?

Assembler directives serve several purposes:

- Control how and where data is stored  
- Define sections of the program (code, data, etc.)  
- Help the assembler and linker organize symbols, constants, and memory alignment  
- Enable modular and maintainable code through labels, constants, and scope control  

These directives do **not** become machine instructions, so they **don’t take up execution time** on the CPU.

---

####  Common RISC-V Assembler Directives

Here’s a list of commonly used directives in RISC-V assembly, with brief explanations:

| **Directive** | **Purpose**                                                                 |
|---------------|------------------------------------------------------------------------------|
| `.align`      | Aligns the memory location to a power-of-2 boundary (e.g., 4, 8, 16).       |
| `.section`    | Defines which section (like `.text`, `.data`, etc.) the following code or data belongs to. |
| `.byte`       | Reserves and optionally initializes 8-bit values.                           |
| `.half`       | Reserves and optionally initializes 16-bit values.                          |
| `.word`       | Reserves and optionally initializes 32-bit values.                          |
| `.data`       | Indicates the start of the **data section** (for initialized variables).     |
| `.text`       | Indicates the start of the **code section** (for actual instructions).       |
| `.globl`      | Declares a symbol (e.g., a function or variable name) as **global**, so it can be accessed from other files. |
| `.equ`        | Assigns a constant value to a symbol; useful for defining named constants.   |
| `.string`     | Defines a null-terminated string of ASCII characters.                        |

---

### 3.1.3 CSR Access in RISC-V

#### What Are CSRs?

**CSR** stands for **Control and Status Register**.  
In RISC-V, each **hart** (hardware thread, like a CPU core) has a set of **4096 CSRs**.

These registers are used to:
- Configure hardware (control registers)
- Report system information (status registers)

**Examples:**
- Timer and performance counters
- Exception handling
- Privilege level info
- Floating-point unit status

---

#### Where Are CSRs Located?

- CSRs are **not part of regular memory**.
- They are located in a **special address space**, with addresses ranging from **0x000 to 0xFFF**.
- You access them using **dedicated instructions**, not regular load/store instructions.

---

#### CSR Access Instructions

These are defined by the **Zicsr** (Control and Status Register) extension in RISC-V.

There are **3 main CSR instructions**:

| Instruction | Meaning   | Behavior                                         |
|-------------|-----------|------------------------------------------------|
| CSRRW       | Read/Write| Reads the CSR, writes a new value from a register |
| CSRRS       | Read/Set  | Reads the CSR, sets bits that are 1 in a register |
| CSRRC       | Read/Clear| Reads the CSR, clears bits that are 1 in a register |

---

#### Two Addressing Modes

Each instruction has **2 versions**, depending on the source operand:

1. **Register version**  
   - Uses a general-purpose register (e.g., `x1`, `x2`, etc.) as the source.

   **Examples:**
   ```assembly
   csrrw x5, 0x300, x6   # Read CSR 0x300 into x5, write x6 into CSR
   csrrs x5, 0xC00, x0   # Read CSR 0xC00 (cycle counter) into x5, don’t set anything (x0 = 0)
   ```

2. **Immediate version** (ends with `I`)  
   - Uses a 5-bit immediate constant instead of a register.  
   - Used for small constant values (like setting a flag).
   **Examples:**
   ```assembly
   csrrwi x5, 0x300, 1   # Write 1 into CSR 0x300, return old value in x5
   csrrsi x0, 0x300, 2   # Set bit 2 in CSR 0x300, discard the read result (x0)
   ```

---

#### Summary: The 6 Zicsr Instructions

| Instruction | Immediate Version | Description             |
|-------------|-------------------|-------------------------|
| CSRRW       | CSRRWI            | Read and write CSR      |
| CSRRS       | CSRRSI            | Read and set bits in CSR|
| CSRRC       | CSRRCI            | Read and clear bits in CSR|

---

#### Common CSRs You Might Use

| CSR Name | Address | Purpose               |
|----------|---------|-----------------------|
| mstatus  | 0x300   | Machine status        |
| misa     | 0x301   | ISA features          |
| mie      | 0x304   | Interrupt enable      |
| mepc     | 0x341   | Exception program counter |
| cycle    | 0xC00   | Cycle counter         |
| time     | 0xC01   | Timer                 |
| instret  | 0xC02   | Instructions retired  |

