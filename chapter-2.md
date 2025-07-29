# Chapter 2: Exploring the RISC-V ISA

## 2.1 RISC-V: A Modular ISA

- **Fifth-generation design**  
  - RISC-V is the result of a research project that began in 1980.  
  - It incorporates lessons learned from previous ISA generations.

- **Designed for flexibility and longevity**  
  - Aims to succeed where previous architectures have failed.  
  - Avoids legacy complexity by learning from past ISA shortcomings.

- **Modular Instruction Set Architecture (ISA)**  
  - Unlike traditional ISAs (e.g., ARM Cortex), which evolve incrementally, RISC-V is modular by design.

- **Core structure**  
  - Consists of a mandatory base ISA (e.g., RV32I, RV64I, or RV128I).  
  - Augmented by a set of optional ISA extensions.

- **ISA extensions**  
  - Extensions include features like:  
    - **M:** Integer multiplication/division  
    - **F/D:** Floating-point support  
    - **A:** Atomic operations  
    - **C:** Compressed instructions  
    - **V:** Vector operations  
  - Extensions can be mixed and matched based on application requirements.

- **Custom CPU tailoring**  
  - Implementers can choose only the necessary features.  
  - Enables efficient designs for everything from microcontrollers to high-performance CPUs.

- **Resulting benefits**  
  - Reduces unnecessary complexity and overhead.  
  - Encourages innovation and adaptability across diverse computing environments.

---

## 2.2 RISC-V ISA Naming Convention

- **General Format:**  
  - `RV` + `<bit-width>` + `<ISA extensions>`  
  - Example: **RV32IMAC**

- **Breakdown of RV32IMAC:**  
  - **RV32I** – 32-bit Base Integer ISA  
    - Indicates a 32-bit CPU.  
    - The **I** extension stands for Integer instructions.  
    - This is the mandatory base ISA and includes fundamental instructions for:  
      - Arithmetic and logic operations  
      - Control flow (jumps, branches)  
      - Memory access (loads and stores)  
  - **M** – Integer Multiplication and Division Extension  
    - Adds support for MUL, DIV, REM, and related operations.  
    - Crucial for arithmetic-heavy workloads like DSP, cryptography, or scientific computing.  
  - **A** – Atomic Instructions Extension  
    - Enables atomic read-modify-write operations.  
    - Used for synchronization primitives (locks, semaphores) in multi-core or multi-threaded environments.  
  - **C** – Compressed Instructions Extension  
    - Adds 16-bit instruction encodings for frequently used operations.  
    - Reduces program size and improves instruction cache utilization.  
    - Useful in memory-constrained systems like embedded devices.

- **Why It Matters:**  
  - This naming convention makes it easy to identify a CPU's capabilities at a glance.  
  - Example: A processor labeled **RV64GC** is a 64-bit CPU with:  
    - Base Integer (I),  
    - General-purpose extensions (M, A, F, D),  
    - Compressed (C) instructions.

![Diagram](./images/RV32IMAC.png)

---

## 2.3 RISC-V ISA: Two-Part Structure

1. **Volume I – Unprivileged Specification**  
   - Defines user-level instructions (e.g., ADD, LW, MUL).  
   - Includes base ISAs (RV32I, RV64I) and standard extensions (M, A, F, etc.).  
   - Used by applications, compilers, and toolchains.  
   - Portable across hardware that supports the same unprivileged ISA.

2. **Volume II – Privileged Specification**  
   - Defines system-level behavior (OS and hypervisor support).  
   - Includes privilege modes (U, S, M), CSRs, traps, memory protection, etc.  
   - Used by OS developers and hardware designers.

---

## 2.4 RISC-V Privilege Levels

RISC-V defines three core privilege levels, similar to CPU protection rings:

| Privilege Level         | Alias   | Typical Use                          | Comparable to    |
|-------------------------|---------|--------------------------------------|------------------|
| Machine Mode (M-mode)   | Ring 0  | Bootloaders, firmware, secure monitor| Supervisor/Root  |
| Supervisor Mode (S-mode)| Ring 1  | OS kernel, device drivers            | Kernel           |
| User Mode (U-mode)      | Ring 2  | Applications, user processes         | User space       |

### Additional Modes:

- **Hypervisor Mode (H-mode)** *(optional)*  
  - Enables virtualization features.  
  - Built as an extension to S-mode.

### Control and Status Registers (CSRs):

- Special registers that control or report system behavior.  
- **Privilege-based access:**
  - Higher modes (e.g., M-mode) can access CSRs from lower modes (e.g., S or U).  
  - Lower modes **cannot** access higher-level CSRs.

---

## 2.5 Overview of the Unprivileged RISC-V Specification

The unprivileged specification in RISC-V defines features that operate independently of Machine Mode (M-Mode) and Supervisor Mode (S-Mode). It includes the base Integer (I) ISA and various extensions such as floating-point (F), double-precision (D), and compressed instructions (C).

### Key Base ISAs:

- **RV32I**: Standard 32-bit integer instruction set  
- **RV32E**: A streamlined version of RV32I for embedded systems  
- **RV64I**: Extends RV32I to 64-bit registers and address space  
- **RV128I**: A 128-bit version for future expansion  

These ISAs either reduce or build upon RV32I. For example, RV64I adapts LOAD and STORE instructions to handle 64-bit data and memory addressing.

---

### 2.5.1 Overview of the RV32I Base Integer ISA

The RV32I base integer instruction set architecture (ISA) is the foundation of the RISC-V ISA, consisting of just **40 essential instructions**. It supports core operations necessary for working with 32-bit integers and includes:

- **Arithmetic**: Addition and subtraction  
- **Logical**: Bitwise operations  
- **Memory access**: Load and store  
- **Control flow**: Jumps and branches  

All instructions are **32 bits wide**, and the ISA defines **32 general-purpose registers**, each **32 bits** wide. In addition to these registers, the CPU also includes a **program counter (PC)**. A special register, **x0**, is hardwired to always read as zero, a common feature in many RISC architectures.

While registers are technically general-purpose, their usage is guided by the **Application Binary Interface (ABI)**. The ABI assigns specific roles to each register based on calling conventions, such as storing arguments, return addresses, temporary variables, or stack/frame pointers.

The RV32I ISA forms the **backbone of RISC-V systems**, ensuring minimal yet powerful functionality for efficient computing.

---

### RISC-V Symbolic Register Names (RV32I ABI Names)

In RISC-V, hardware registers are named **x0 to x31**, but they are also given **symbolic (ABI) names** to indicate their conventional use in high-level programming. These symbolic names improve code **readability** and follow a standardized **calling convention**.

| Register   | ABI Name | Description                             |
|------------|----------|-----------------------------------------|
| x0         | zero     | Constant 0 (always reads 0)             |
| x1         | ra       | Return address                          |
| x2         | sp       | Stack pointer                           |
| x3         | gp       | Global pointer                          |
| x4         | tp       | Thread pointer                          |
| x5–x7      | t0–t2     | Temporary registers                     |
| x8         | s0/fp    | Saved register / Frame pointer          |
| x9         | s1       | Saved register                          |
| x10–x11    | a0–a1     | Function arguments / return values      |
| x12–x17    | a2–a7     | Function arguments                      |
| x18–x27    | s2–s11    | Saved registers                         |
| x28–x31    | t3–t6     | Temporary registers                     |

#### Notes:

- **zero (x0)**: Always reads 0; writing to it has no effect.  
- **ra (x1)**: Stores the return address when a function is called.  
- **sp (x2)**: Points to the top of the current stack.  
- **gp (x3)** and **tp (x4)**: Used for global and thread-local storage.  
- **t0–t6**: Temporary registers, not preserved across function calls.  
- **s0–s11**: Saved registers, must be preserved by the callee.  
- **a0–a7**: Argument registers for function calls; `a0` and `a1` also hold return values.

---

### 2.5.2 Overview of Control and Status Registers (CSRs) in RISC-V

Control and Status Registers (CSRs) are a dedicated set of up to **4096 special-purpose registers** in RISC-V, used to control and monitor processor behavior. They exist in a separate **12-bit address space** and store crucial CPU data such as timers, interrupt status, flags, and hardware details.

#### Purpose of CSRs:
CSRs allow software to:
- Manage processor settings  
- Handle exceptions and interrupts  
- Access system-level information  

The **Zicsr extension** defines instructions for interacting with CSRs:
- `CSRRW`: Read and write  
- `CSRRS`: Read and set bits  
- `CSRRC`: Read and clear bits  

These instructions allow **efficient and safe CSR access** and modification.

#### Common Key CSRs:

- **`mstatus`**: Controls interrupt enable flags and privilege modes  
- **`mepc`**: Holds the program counter at the time of an exception or interrupt  
- **`mtvec`**: Sets the base address for machine-mode trap handlers  
- **`mcause`**: Identifies the cause of the most recent exception or interrupt  
- **`misa`**: Describes supported ISA features and base ISA width (RV32, RV64, or RV128)

These CSRs are essential for:
- Privilege mode transitions  
- Trap and interrupt handling  
- Discovering hardware capabilities  

---

#### Key CSRs in Detail:

1. **`mstatus` – Machine Status Register**
   - Controls and reflects the current status of the processor.  
   - Contains flags and fields for:
     - Global interrupt enable/disable (e.g., `MIE`, `SIE`)  
     - Current and previous privilege modes (`MPP`, `SPP`)  
     - Per-level interrupt enable status  
   - Essential for managing privilege transitions, interrupts, and context switches.

2. **`mepc` – Machine Exception Program Counter**
   - Stores the address of the instruction that caused an exception or interrupt.  
   - Used with `MRET` to resume execution after a trap.  
   - Ensures proper control flow resumption post-interrupt.

3. **`mtvec` – Machine Trap-Vector Base Address**
   - Points to the base address of the machine-mode trap handler.  
   - Supports two modes:  
     - **Direct**: All traps go to one entry point  
     - **Vectored**: Offset used based on the exception cause  
   - Enables flexible trap/interrupt vectoring.

4. **`mcause` – Machine Cause Register**
   - Identifies the reason for the most recent trap.  
   - Contains:
     - Interrupt vs. exception flag  
     - Cause code (e.g., illegal instruction, timer interrupt)  
   - Vital for building trap diagnostics and exception handlers.

5. **`misa` – Machine ISA Register**
   - Describes the processor’s supported features.  
   - Encodes:
     - Base ISA width (`RV32`, `RV64`, `RV128`)  
     - Extensions via a bitmask (e.g., `I`, `M`, `F`, `D`, `C`)  
   - Helps software determine feature availability at runtime.

---

### 2.5.3 ISA Extensions in RISC-V

In RISC-V, the base ISA (e.g., RV32I) defines the **minimal instruction set** required for a functional CPU. However, most real-world applications need additional capabilities such as floating-point arithmetic, atomic instructions, or cryptographic operations.

#### What Are ISA Extensions?

ISA extensions are **optional**, modular additions to the base instruction set. They provide **specialized capabilities** and are described in the **Unprivileged Specification** if they don’t require M-mode access.

#### Who Develops Extensions?

Extensions are developed by task groups under **RISC-V International**, consisting of domain experts and contributors. These extensions are ratified before becoming standard.

##### Notable Task Groups and Extensions:

- **Crypto Task Group**
  - Adds hardware-accelerated cryptographic operations  
  - Useful for secure applications like IoT and secure boot

- **B Extension Task Group**
  - Focuses on **bit manipulation** instructions  
  - Includes counting, rotation, setting/clearing bits

- **Vector (V) Extension Task Group**
  - Provides **vector/SIMD-style processing**  
  - Enhances performance in AI/ML, signal processing, and HPC

#### Ratification Process:

Once ratified, an extension becomes part of the **official RISC-V ISA**, allowing consistent hardware and software support across implementations.

---

### 2.5.4 The M Extension for Multiplication and Division

The **M Extension** adds **hardware support for integer multiplication and division**, which are not included in the base ISA.

#### Features of the M Extension:

- **RV32M** (for 32-bit systems): Adds 8 instructions  
  - Multiplication: `MUL`, `MULH`, `MULHSU`, `MULHU`  
  - Division: `DIV`, `DIVU`, `REM`, `REMU`

- **RV64M** (for 64-bit systems): Adds 5 additional instructions to support 64-bit arithmetic

#### How It Works:

- Operands come from general-purpose registers  
- Results are written back to destination registers  
- Specifies signed/unsigned behavior, handling of overflow, and divide-by-zero

#### Why It’s Optional:

- Hardware multiplication/division **adds complexity and power consumption**  
- For embedded systems:
  - Operations can be implemented in software  
  - Helps reduce silicon area and cost  
  - Improves energy efficiency

RISC-V leaves this extension optional to enable **flexibility and optimization** for application-specific CPUs.

---

### 2.5.5 The F and D Extensions for Floating-Point Arithmetic

RISC-V includes optional extensions for **floating-point math**:
- **F**: Single-precision (32-bit)
- **D**: Double-precision (64-bit)

These follow the **IEEE 754 standard**.

#### F Extension – Single Precision

- Adds **32 registers**: `f0` to `f31`, each 32 bits  
- Instructions include:
  - Arithmetic: `FADD.S`, `FSUB.S`, `FMUL.S`, `FDIV.S`  
  - Conversion: Between int and float  
  - Comparisons and rounding  
  - Handling `NaN`, `+∞`, `-∞`  
- Useful for:
  - Graphics  
  - Gaming  
  - Basic signal processing  

#### D Extension – Double Precision

- Builds on the F extension  
- Same 32 registers now interpreted as **64-bit wide**  
- Instructions include:
  - `FADD.D`, `FDIV.D`, etc.  
  - Conversions among int, float, and double  
  - Full IEEE handling of special values  
- Crucial for:
  - Scientific computing  
  - Financial modeling  
  - High-precision engineering simulations

#### Why Are These Extensions Optional?

- Floating-point units (FPUs) **consume area and power**  
- Embedded systems may:
  - Omit FPUs to save resources  
  - Use **software emulation** instead  
- RISC-V makes these optional to allow **tailored CPU design** based on use case

---

### 2.5.6 RISC-V's C Extension for Compressed Instructions

The **C Extension** adds 16-bit compressed versions of certain instructions to the RISC-V instruction set, known as **RVC (RISC-V Compressed Instructions)**. These compressed instructions are selected based on their frequency of use in real-world applications, allowing for reduced code size and improved efficiency.

#### Why is the C Extension Important?

- **Code Size Reduction**: The use of 16-bit versions of frequently used instructions helps reduce the overall size of a program, saving memory and improving cache utilization.
- **Static & Dynamic Efficiency**: The compression benefits both the compiled code (static) and runtime code (dynamic), leading to faster execution and better memory management.

#### How Does it Work?

- RISC-V designers studied modern compilers to identify the most frequently used instructions and created **34 16-bit versions** to replace their 32-bit counterparts.
- **Key Trade-off**: While the 16-bit instructions are more compact, they do not include all features of their 32-bit counterparts. However, basic functionality is preserved, and the original 32-bit instructions are still available for use when needed.

#### How Much Space Does it Save?

- **50%–60%** of instructions in a typical RISC-V program can be replaced by the 16-bit compressed instructions.
- This compression leads to a **25%–30% reduction in code size**, making programs more efficient.

#### Compatibility

- The **RVC 16-bit instructions** can be mixed freely with the **32-bit instructions**. This means no alignment issues occur, and the processor can handle them without throwing exceptions about "instruction-address-misaligned."
- The **C extension** is fully compatible with other standard RISC-V extensions, so it can be easily integrated into existing systems.

#### Why 16-bit?

The 16-bit compressed instructions are possible due to several key factors:
- **Registers**: Certain registers are used more frequently, allowing the instructions to assume specific registers without specifying all 32 possible registers.
- **Operands**: In many RISC-V programs, one operand is often overwritten, reducing the need for multiple operands.
- **Immediate Values**: The compressed instructions make use of smaller immediate values, allowing them to fit within a 16-bit encoding.

#### The Compression Process

- By limiting the registers, reducing the number of operands (2 instead of 3), and using smaller immediate values, the instructions can be compressed into just 16 bits.
- This compression results in smaller programs without sacrificing significant functionality.

![Diagram](./images/RV32IMAC.png)

In the referenced image, the difference between **RV32I** (32-bit RISC-V instructions) and **RV32C** (16-bit compressed instructions) is clearly shown. RV32I instructions are 32 bits wide, while RV32C instructions are only 16 bits wide, making them more efficient in terms of memory usage and faster access to frequently used operations.

---

### C Extension vs. Pseudoinstructions

**No**, instructions from the C extension are **not** pseudoinstructions.

- **Pseudoinstructions**: These are software additions to assembly language that simplify programming. They have a direct machine code translation and are supported by assemblers and compilers.
- **C Extension**: This adds hardware-supported versions of existing base ISA instructions to reduce code size, involving both **software** and **hardware** changes.

In summary, pseudoinstructions are **software-only**, while the **C extension** involves both **software and hardware** support for instruction compression.

---

### 2.5.7 Overview of Additional RISC-V Extensions

RISC-V’s **open and modular design** allows for the creation of a variety of extensions that provide specialized capabilities, making it suitable for a wide range of applications.

#### Key RISC-V Extensions

1. **A Extension (Atomic Memory Operations)**:
   - Provides atomic memory operations for multi-core systems to avoid conflicts when multiple processors access shared memory.

2. **Q Extension (Quad-Precision Floating-Point)**:
   - Introduces **128-bit floating-point registers** to support high-precision operations, essential for scientific calculations.

3. **B Extension (Bit Manipulation)**:
   - Adds instructions for efficient bit-level operations, such as bitwise operations, which are useful for cryptography and data compression.

4. **S Extension (Supervisor Operations)**:
   - Introduces supervisor-level operations that control system resources, key for managing OS transitions and privileged modes.

5. **H Extension (Hypervisor Operations)**:
   - Supports **hypervisor operations** to create virtual machines and efficiently manage hardware resources in virtualized environments.

6. **L Extension (Decimal Floating-Point Operations)**:
   - Adds support for **decimal floating-point** operations, critical for applications like financial systems that require precise decimal rounding.

7. **P Extension (Packed-SIMD Instructions)**:
   - Introduces **Packed-SIMD** instructions for parallel data processing, boosting performance in image processing, machine learning, and multimedia tasks.

8. **V Extension (Vector Operations)**:
   - Adds vector processing capabilities for high-performance computing tasks like scientific calculations and machine learning.

9. **Zicsr Extension (Control and Status Register Manipulation)**:
   - Provides operations for manipulating **Control and Status Registers (CSRs)**, which are crucial for low-level hardware management.

10. **Zifencei Extension (Instruction Memory Synchronization)**:
    - Ensures proper synchronization of instruction memory, which is crucial for systems that use instruction prefetching or out-of-order execution.

---

### 2.5.8 Unsupported Instructions in RISC-V

If a RISC-V processor encounters an instruction it doesn't support (e.g., an RV32IAC processor without the M extension trying to execute a multiplication), it triggers an **illegal instruction exception**.

- **Compilers** are aware of the CPU's extensions and generate code accordingly.
- If an unsupported instruction is encountered, the software **handles the exception**, often by emulating the instruction or using an alternative from the standard library.

---

## 2.6 RV32I Instruction Encoding Overview

The **RV32I Instruction Set Architecture (ISA)** defines a 32-bit system with the following key elements:

- A **32-bit Program Counter (PC)** and **32 general-purpose 32-bit registers** (`x0` to `x31`).
- **40 unprivileged 32-bit instructions**, organized into six formats (**R, I, S, B, U, J**), all sharing common fields:
  - A **7-bit major opcode**.
  - **Source registers**:
    - `rs1` in bits **15–19**
    - `rs2` in bits **20–24**
  - **Destination register** (`rd`) in bits **7–11**
  - **Function fields**:
    - `funct3` in bits **12–14**
    - `funct7` in bits **25–31** (for R-type)
  - **Immediate fields**, placed differently based on instruction type.
- **24 privileged 32-bit instructions**, defined using only **R** and **I** formats.
- A key principle of RISC-V emphasized in this ISA is the **fixed instruction length**:
  > All instructions are encoded in **32 bits**, with **no exceptions**.

![Diagram](./images/ISA_format.png)

---

### 2.6.1 Instruction Encodings with Explanations

| Type | Use                      | Example               | Used For                             |
|------|---------------------------|------------------------|---------------------------------------|
| R    | Register operations       | `add x1, x2, x3`       | Arithmetic, logic, shifts             |
| I    | Immediate & load          | `addi x1, x2, 10`      | Constants, memory loads               |
| S    | Store to memory           | `sw x1, 0(x2)`         | Memory writes                         |
| B    | Conditional branch        | `beq x1, x2, label`    | Loops, conditionals                   |
| U    | Upper immediate (20 bits) | `lui x1, 0x12345`      | Large constants, addresses            |
| J    | Unconditional jump        | `jal x1, label`        | Function calls, jumps                 |

---

#### 1. R-Type (Register Type)

- **Purpose:** Performs arithmetic and logical operations between two registers.
- **Fields include:** Two source registers (`rs1`, `rs2`), one destination register (`rd`), function codes (`funct3`, `funct7`), and opcode.
- **Example:** `add x1, x2, x3` — adds contents of `x2` and `x3`, stores result in `x1`.
- **Used for:** Arithmetic (`add`, `sub`), bitwise (`and`, `or`), and shift operations on registers.

---

#### 2. I-Type (Immediate Type)

- **Purpose:** Operates with a register and a constant immediate value; also used for loads and system calls.
- **Fields include:** One source register (`rs1`), destination register (`rd`), 12-bit immediate, `funct3`, and opcode.
- **Example:** `addi x1, x2, 10` — adds immediate `10` to `x2`, stores in `x1`.
- **Used for:** Arithmetic with constants, loading data from memory (`lw`), and system instructions (`ecall`).

---

#### 3. S-Type (Store Type)

- **Purpose:** Stores data from a register into memory at an address computed from a base register plus an immediate offset.
- **Fields include:** Two parts of a 12-bit immediate split around source registers (`rs1`, `rs2`), `funct3`, and opcode.
- **Example:** `sw x1, 0(x2)` — stores value from `x1` into memory at address in `x2`.
- **Used for:** Writing register data to memory.

---

#### 4. B-Type (Branch Type)

- **Purpose:** Controls program flow by conditionally branching to a target address if a condition on registers is met.
- **Fields include:** Two source registers (`rs1`, `rs2`), a 12-bit immediate (used for branch target calculation), `funct3`, and opcode.
- **Example:** `beq x1, x2, label` — branches to `label` if `x1` equals `x2`.
- **Used for:** Implementing loops, conditional statements, and decision making.

---

#### 5. U-Type (Upper Immediate Type)

- **Purpose:** Loads a large 20-bit immediate into the upper 20 bits of a register, zeroing the lower bits.
- **Fields include:** 20-bit immediate and destination register (`rd`), plus opcode.
- **Example:** `lui x1, 0x12345` — loads `0x12345000` into `x1`.
- **Used for:** Handling large constants and addresses, essential for position-independent code.

---

#### 6. J-Type (Jump Type)

- **Purpose:** Performs unconditional jumps to a target address, saving the return address for function calls.
- **Fields include:** A 20-bit immediate for jump target, destination register (`rd`), and opcode.
- **Example:** `jal x1, label` — jumps to `label` and saves return address in `x1`.
- **Used for:** Function calls and control transfer.

---

### 2.6.2 Immediates and Addresses in RISC-V Instructions

In RISC-V, most instructions other than R-Type include **immediates** — literal values encoded directly inside the 32-bit instruction word instead of being stored in registers or memory.

#### 2.6.2.1 What Are Immediates?

- Immediates are fixed data embedded within an instruction.
- They can represent:
  - Constants used in arithmetic or logic (e.g., add immediate).
  - Memory addresses or offsets for loads, stores, branches, and jumps.

#### 2.6.2.2 Why Do Immediates Matter?

- They allow instructions to operate on values or addresses without extra memory access.
- Using immediates speeds up common operations like pointer arithmetic, branch targeting, and loading constants.

#### 2.6.2.3 Immediate Encoding Characteristics

- All immediates decode to **32-bit values** in the processor, regardless of their bit-length in the instruction.
- Encoding varies by instruction type to optimize the instruction layout and functionality.
- The immediate fields are placed mostly on the left (higher bits) side of the instruction but are arranged differently per type.
- This variation defines the instruction types (I, S, B, U, J) and how immediates are extracted.

#### 2.6.2.4 How Immediate Encoding Differs by Instruction Type

| Instruction Type | Immediate Size | Immediate Purpose          | Encoding Notes                                      |
|------------------|----------------|----------------------------|----------------------------------------------------|
| I-Type           | 12 bits        | Constants, load offsets    | Continuous 12-bit immediate in bits [31:20]        |
| S-Type           | 12 bits        | Store address offset       | Split into two parts: bits [31:25] and bits [11:7] |
| B-Type           | 12 bits        | Branch target offset       | Immediate bits scattered for sign extension, alignment |
| U-Type           | 20 bits        | Upper 20 bits of a constant| Immediate occupies bits [31:12], lower 12 bits zeroed |
| J-Type           | 20 bits        | Jump target offset         | Immediate bits spread non-linearly for sign extension |

##### Example: Immediate Positions in Common Types

- **I-Type Immediate:**  
  Encoded as a single 12-bit field `[31:20]` — directly sign-extended to 32 bits.

- **S-Type Immediate:**  
  Split between bits `[31:25]` and `[11:7]` — concatenated and sign-extended.

- **B-Type Immediate:**  
  Bits are taken from `[31]`, `[7]`, `[30:25]`, `[11:8]` and combined with shifting to form the branch offset.

- **U-Type Immediate:**  
  A 20-bit immediate in `[31:12]`, representing bits 31 to 12 of the final value, with lower 12 bits zeroed.

- **J-Type Immediate:**  
  Bits from `[31]`, `[19:12]`, `[20]`, and `[30:21]` combined and shifted for jump target.

> Despite different bit placements, all immediates are sign- or zero-extended to 32 bits before use.

#### 2.6.2.5 What Does “Extended to 32 bits” Mean?

In RISC-V, immediates inside instructions often have fewer than 32 bits (for example, 12 or 20 bits). But the processor uses 32-bit registers, so these shorter immediates must be converted, or "**extended,**" to full 32-bit values before use.

There are two common types of extension:

##### 1 Sign Extension

- If the immediate represents a **signed number** (which can be positive or negative), its most significant bit (MSB) — the leftmost bit of the immediate — is copied (or “replicated”) into the higher bits up to 32 bits.
- This preserves the number's sign and value when expanding to 32 bits.

**Example:**  
A 12-bit immediate: `0b1111_1111_1010` (which is negative in 12-bit two’s complement)  
When sign-extended to 32 bits, it becomes:  
`0b1111_1111_1111_1111_1111_1111_1111_1010` (still negative, same value)

##### 2 Zero Extension

- If the immediate is always **non-negative (unsigned)**, the upper bits are simply filled with zeros.

**Example:**  
A 20-bit immediate: `0x12345` becomes  
`0x00012345` when zero-extended to 32 bits.

##### 2.6.2.6 Why is Extension Needed?

- CPU registers and arithmetic units work with **32-bit data**.
- The immediate must be the same width as registers before performing operations.
- Proper extension ensures correct signed or unsigned arithmetic.

---
### 2.6.3 Instruction Mnemonics and Their Encoding

In RISC-V assembly language, **instruction mnemonics** are used as human-readable shorthand for machine instructions. Each mnemonic represents a specific operation that the processor can perform.

For example, the mnemonic `add` corresponds to an **addition operation**.

A typical instruction like:  `add x1, x2, x3`
means: "Add the contents of register x2 and register x3, then store the result in register x1."

#### 2.6.3.1 Role of the Assembler

The assembler translates these mnemonics into machine code, which is the binary language the processor understands. For the `add` instruction, the assembler produces a 32-bit machine code word.  

In hexadecimal, this might be: ``0x003100B3``  
which corresponds to the binary number:  ``0000000 00011 00010 000 00001 0110011``  

##### R-Type Instruction Format

| Bit Positions   | 31‒25   | 24‒20  | 19‒15  | 14‒12 | 11‒7   | 6‒0     |
|-----------------|---------|--------|--------|--------|--------|---------|
| **Field Names** | funct7  | rs2    | rs1    | funct3 | rd     | opcode  |
| **Field Values**| 0000000 | 00011  | 00010  | 000    | 00001  | 0110011 |
| **Register Names** |         | x3     | x2     |        | x1     | `add`   |

##### Breakdown of the `add x1, x2, x3` Encoding

- **funct7 (0000000)**: Specifies the operation variant — here, `add`.
- **rs2 (00011)**: Register `x3` (source operand 2).
- **rs1 (00010)**: Register `x2` (source operand 1).
- **funct3 (000)**: Further operation code specifying addition.
- **rd (00001)**: Destination register `x1`.
- **opcode (0110011)**: Indicates an R-type instruction.
