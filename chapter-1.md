# Chapter 1: Introduction

## Key Differences Between RISC and CISC:

| **Feature**             | **RISC (Reduced Instruction Set Computer)**                  | **CISC (Complex Instruction Set Computer)**                     |
|-------------------------|-------------------------------------------------------------|------------------------------------------------------------------|
| **Instruction Set**      | Small, simple set of instructions                           | Large, complex set of instructions                              |
| **Registers**            | Many registers (to avoid memory access)                     | Fewer registers (many instructions access memory directly)      |
| **Memory Access**        | Memory access restricted to Load/Store instructions         | Memory access is available with many instructions (directly)    |
| **Instruction Length**   | Fixed instruction size (usually 32 bits)                    | Variable instruction size (could be 1-15 bytes)                |
| **Execution Time**       | Uniform instruction execution time (faster, simpler pipelines) | Variable execution time (due to complex instructions)           |
| **Pipelining**           | Easy to pipeline (due to simpler instructions)              | Harder to pipeline due to complex instruction decoding          |
| **Power Consumption**    | Generally lower (less complex, simpler operations)          | Can be higher (due to more complex instructions and memory ops) |
| **Examples**             | ARM, MIPS, RISC-V                                           | x86, Intel, AMD                                                 |

### CISC Instruction Sets:
- Support many instructions.
- Often include complex instructions for specific tasks.
- Example: **Intel 80386** (1985) had over 150 instructions.

### RISC Instruction Sets:
- Have a modest number of simple, general-purpose instructions.
- Example: **RISC-V Base Integer ISA** (2010) has only 40 instructions.

---

## RISC-V License

The **RISC-V ISA** (Instruction Set Architecture) is licensed under a **permissive open-source license**. This means anyone can use, implement, and distribute RISC-V without licensing fees or royalty payments. The open nature of the ISA allows for both commercial and non-commercial implementations, including both open-source and proprietary designs.

---

## RISC-V Name Origin

The name **RISC-V** was chosen to represent the fifth major RISC ISA design from **UC Berkeley**, following earlier designs: **RISC-I**, **RISC-II**, **SOAR**, and **SPUR**. The **"V"** also plays on the Roman numeral for 5, symbolizing **"variations"** and **"vectors"**, as the ISA aims to support a range of architecture research, including data-parallel accelerators.

