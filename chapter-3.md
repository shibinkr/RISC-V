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

---

### 3.1.4 Labels in Assembly Language

---

#### What Are Labels?

In assembly language, **labels** are names that refer to specific memory locations in your code. They make your code:

- Easier to read and understand  
- Easier to maintain  
- Free from hardcoding memory addresses  

A label is usually followed by a colon `:` and is placed before an instruction or data element.

---

#### How Labels Work (Behind the Scenes)

When the assembler processes your code:

- It records the memory address of the instruction or data that follows the label.  
- It then replaces the label with that address wherever the label is used.  

So, a label is essentially like a **named bookmark** for a memory address.

---

#### Example – Instruction Labels

```assembly
loop:
  add x1, x2, x3
  addi x1, x1, 1
  j loop
```
- `j loop` tells the CPU to **jump back** to the address marked by the `loop` label.
- The assembler calculates the **offset** from the jump instruction to the label and encodes it into the `j` instruction.

> **Note:** This helps avoid manually managing instruction addresses or offsets.

---

#### Example – Data Labels

```assembly
.data
count:  .word   0
array:  .half   6, 5, 5, 3, 2, 1
msg:    .string "Hey there"
age:    .byte   21
```
##### What Happens Here?

- `.data` tells the assembler you're defining **static data**.
- The assembler assigns **consecutive addresses** for each data element based on their size.

##### Data Label Breakdown

| Label | Data Type | Description                                                  | Address (Relative)       |
|-------|-----------|--------------------------------------------------------------|---------------------------|
| `count` | `.word`    | 4 bytes (32-bit) initialized to 0                           | Starts at `.data` section |
| `array` | `.half`    | 6 half-words (12 bytes total), values: 6, 5, 5, 3, 2, 1     | `count` address + 4       |
| `msg`   | `.string`  | Null-terminated string `"Hey there"` (10 bytes including `\0`) | `array` address + 12      |
| `age`   | `.byte`    | A single byte initialized to 21                            | `msg` address + 10        |

---

##### Accessing These Labels in Code

```assembly
la a0, msg      # Load address of "msg" into register a0
lw t0, count    # Load word from label "count"
```

#### Syntax Considerations

- Most assemblers require labels to:
  - End with a colon `:` (e.g., `start:`)
  - Begin at the **start of a line** (no indentation)

- Different assemblers (e.g., **GAS**, **NASM**) may have slight syntax variations.

---

### 3.1.5 Understanding Immediate Sizes in RISC-V RV32I

#### What is an Immediate?

An **immediate** is a constant value encoded directly within an instruction—rather than being stored in a register or memory.

---

#### Immediate Sizes in RV32I

The RISC-V RV32I (base 32-bit integer) architecture supports different immediate sizes depending on the instruction type. Here's how they break down:

---

##### Common Size: 12-bit Immediates

Most arithmetic and logical instructions (e.g., `addi`, `andi`, `ori`) use **12-bit signed immediates**.

**Example:**  
```assembly
addi x1, x2, 100   # Adds 100 (sign-extended to 32 bits) to x2 and stores the result in x1.
```

- The value `100` is encoded in 12 bits.
- It is sign-extended to 32 bits to match the register width.

---

##### 20-bit Immediates

Some instructions use larger immediates:

- **`lui` (Load Upper Immediate):** Uses 20 bits.
  - Loads the value into the upper 20 bits of a register.
  - The lower 12 bits are set to zero.

**Example:**  
```assembly
lui t0, 0x12345  # Loads 0x12345000 into t0.
```
---

##### Loading Full 32-bit Immediates

RISC-V does not have a single native instruction for loading an arbitrary 32-bit value. Instead, you combine instructions:

```assembly
lui t0, 0x12345  # Loads the upper 20 bits.
addi t0, t0, 0x678  # Adds the lower 12 bits.
```

Together, this sequence loads `0x12345678` into register `t0`.

> **Note:**  
> RISC-V provides a pseudoinstruction `li` (load immediate) that abstracts this:  
```assembly
  li t0, 0x12345678  
```
> The assembler automatically expands `li` into `lui + addi` or another valid equivalent.

---

#### Jump Instructions and Immediates

- `jal` (Jump and Link) and `jalr` (Jump and Link Register) use **20-bit signed immediates**.
- These offsets are added to the **Program Counter (PC)** to calculate target jump addresses.

---

#### Always Check the Manual

Since immediate sizes vary by instruction:

- Refer to the **RISC-V Instruction Set Manual**.
- Also check your **assembler’s documentation**, especially for how pseudoinstructions are expanded.

---

#### What About RV64I and RV128I?

These are extended architectures of RV32I:

- **RV64I (64-bit):** Supports up to 20-bit and 32-bit immediates.
- **RV128I (128-bit):** Supports even larger immediate values.

---

#### Summary

| Instruction Type          | Immediate Size | Example                |
|---------------------------|----------------|------------------------|
| Arithmetic (`addi`)       | 12 bits        | `addi x1, x2, 100`     |
| Load Upper (`lui`)        | 20 bits        | `lui t0, 0x12345`      |
| Jump (`jal`, `jalr`)      | 20 bits        | `jal x1, offset`       |
| Pseudoinstruction (`li`)  | Varies         | `li t0, 0x12345678`    |

---

### 3.1.6 What Are Environment Calls?

Environment calls (also known as **system calls**) are a way for a program (like your RISC-V assembly code) to ask the **runtime environment** (like the operating system or simulator) to do something that the program can’t do on its own—like printing to the screen, reading input, or ending the program.

Think of it like this:  
Your assembly code is saying, *“Hey environment, I need your help to do this task.”*

---

#### How Does It Work in RISC-V (Venus Simulator)?

RISC-V uses a special instruction for environment calls:

```assembly
ecall
```

This `ecall` doesn’t do anything by itself unless you:

1. Put the call number in `a0` to tell it *what* you want.  
2. Use `a1` (and sometimes others) to pass arguments (like a number to print).

---

#### Venus Simulator Environment Calls

Here are some key ones:

| `a0` Value | Function              | Argument (in `a1`)            |
|------------|-----------------------|-------------------------------|
| 1          | Print integer         | Integer value to print        |
| 4          | Print string          | Address of string             |
| 11         | Print ASCII character | ASCII code of character       |
| 10         | Exit program          | None                          |

---

#### Example Code Breakdown

```assembly
addi a0, x0, 1 # a0 = 1, this means "print integer"
addi a1, x0, 42 # a1 = 42, this is the number to print
ecall # make the environment call
```

#### Let’s walk through it:

1. `addi a0, x0, 1` → sets `a0 = 1`, so we’re saying, “I want to print an integer.”  
2. `addi a1, x0, 42` → sets `a1 = 42`, so the value we want to print is 42.  
3. `ecall` → triggers the environment to carry out the request.

**The result:** The number **42** gets printed in the terminal.

