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
  - RV + `<bit-width>` + `<ISA extensions>`  
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

| Privilege Level        | Alias   | Typical Use                        | Comparable to    |
|-----------------------|---------|----------------------------------|------------------|
| Machine Mode (M-mode)  | Ring 0  | Bootloaders, firmware, secure monitor | Supervisor/Root  |
| Supervisor Mode (S-mode)| Ring 1  | OS kernel, device drivers         | Kernel           |
| User Mode (U-mode)     | Ring 2  | Applications, user processes       | User space       |

- **Additional Modes:**  
  - **Hypervisor Mode (H-mode)** *(optional)*  
    - Enables virtualization features.  
    - Built as an extension to S-mode.

- **Control and Status Registers (CSRs):**  
  - Special registers that control or report system behavior.  
  - **Privilege-based access:**  
    - Higher modes (e.g., M-mode) can access CSRs from lower modes (e.g., S or U).  
    - Lower modes cannot access higher-level CSRs.
