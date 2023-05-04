## 1 Hello,World of Assembly Language

### 1.1 Requirements:

- a 64-bit version of **MASM** (remember to **configure the environment varibale of ml64.exe**)
-  a linker
- a C++ compiler

### 1.2 The Anatomy of a MASM Program

- A typical MASM program contains **one or more sections** representing  the type of data appearing in memory. These sections begin with a MASM statement such as:

  ~~~assembly
  .CODE(.code);declare machine instructions appear in procedures
  .DATA(.data);declare variables and other memory values
  ~~~

  **Notes:**

  1. The **.code** statement is an example of an assembler directive—a statement that tells MASM something about the program but is **not an actual x86-64 machine instruction**.
  2. the **.code** directive tells MASM to **group the statements following it into a special section of memory reserved for machine instructions**.

- here is a simple assembly source file:

  ~~~assembly
  ; asm_struct.asm
  ; The ".code" directive tells MASM that the statements following
  ; this directive go in the section of memory reserved for machine
  ; instructions (code)	
  	.code
  
  ; Here is the "main" function.
  main PROC
  ; Machine instructions go here
  	ret;Returns to caller
  main ENDP
  ; The END directive marks the end of the source file.
  	end
  ~~~

  run the above codes with the following directives:

  ~~~shell
  ml64 asm_struct.asm /link /subsystem:console /entry:main
  ~~~

  then a **executable** asm_struct.exe will be generated.

  **Note:**

  1. This command tells MASM to assemble the asm_struct.asm program to an **executable** file.
  2. **link the result to produce a console** application
  3. **begin execution at the label main** in the assembly language source file.

### 1.3 Running MASM/C++ Hybrid Program

- here is a MASM source file "asmFunc.asm":

  ~~~assembly
  option casemap:none;make all symbols case-sensitive
  
  public asmFunc;make asmFunc visible outside the source/object file
  
  asmFunc PROC
  ;Machine instructions go here
   
   ret ; Returns to caller
   
  asmFunc ENDP
  ; The END directive marks the end of the source file.
   END
  ~~~

  **Note:**

  1. The **option statement tells MASM to make all symbols case-sensitive**. This  is necessary because MASM, **by default, is case-insensitive and maps all identifiers to uppercase**, however, **C++ is a casesensitive** language and treats asmFunc() and ASMFUNC() as two **different identifiers**.
  2. The **public** statement declares that the asmFunc() identifier will be **visible outside the MASM source/object file**. Without this statement, asmFunc() would be accessible only within the MASM module, and the C++ compilation would complain that asmFunc() is an undefined identifier.

- here is a test cpp source file "assembly.cpp":

  ~~~cpp
  #include <iostream>
  // extern "C" namespace prevents "name mangling" by the C++
  // compiler
  extern "C" {
      void asmFunc(void);
  }
  
  int main()
  {
      std::cout << "Calling asmMain:\n";
      asmFunc();
      std::cout << "Returned from asmMain\n";
      system("pause");
  }
  ~~~

  **Note:**

  1. **name mangling**: C++ will automatically modify the Function name by **adding additional characters**.
  2. **extern "C"** namespace **prevents "name mangling"** by the C++ compiler.

- run the above codes with the following directives:

  ~~~
  ml64 /c asmFunc.asm
  cl assembly.cpp asmFunc.obj
  ~~~

  **Note:**

  1. The ml64 command uses the **/c** option, which stands for **compile-only**, and  does not attempt to run the linker, the **output from MASM is an object code file**.
  2. The **cl command runs the MSVC compiler** on the assembly.cpp file and **links in the assembled code (asmFunc.obj)**. The output from the MSVC compiler is the assembly.exe executable file.

### 1.4 Introduction to the Intel x86-64 CPU Family

- The x86-64 CPU registers can be broken into four categories:

  - general-purpose registers
  - special-purpose application-accessible registers
  - segment registers
  - and special-purpose kernel-mode registers

- The x86-64 (Intel family) CPUs provide several **general-purpose registers** for application use. These include the following:

  - **Sixteen 64-bit registers** that have the following names: RAX, RBX, RCX,  RDX, RSI, RDI, RBP, RSP, R8, R9, R10, R11, R12, R13, R14, and R15.
  - **Sixteen 32-bit registers**: EAX, EBX, ECX, EDX, ESI, EDI, EBP, ESP,  R8D, R9D, R10D, R11D, R12D, R13D, R14D, and R15D
  - **Sixteen 16-bit registers**: AX, BX, CX, DX, SI, DI, BP, SP, R8W, R9W,  R10W, R11W, R12W, R13W, R14W, and R15W
  - **Twenty 8-bit registers**: AL, AH, BL, BH, CL, CH, DL, DH, DIL, SIL,  BPL, SPL, R8B, R9B, R10B, R11B, R12B, R13B, R14B, and R15B

  registers are shown in the following table:

  | Bits 0–63 | Bits 0–31 | Bits 0–15 | Bits 8–15 | Bits 0–7 |
  | :-------: | :-------: | :-------: | :-------: | :------: |
  |    RAX    |    EAX    |    AX     |    AH     |    AL    |
  |    RBX    |    EBX    |    BX     |    BH     |    BL    |
  |    RCX    |    ECX    |    CX     |    CH     |    CL    |
  |    RDX    |    EDX    |    DX     |    DH     |    DL    |
  |    RSI    |    ESI    |    SI     |           |   SIL    |
  |    RDI    |    EDI    |    DI     |           |   DIL    |
  |    RBP    |    EBP    |    BP     |           |   BPL    |
  |    RSP    |    ESP    |    SP     |           |   SPL    |
  |    R8     |    R8D    |    R8W    |           |   R8B    |
  |    R9     |    R9D    |    R9W    |           |   R9B    |
  |    R10    |   R10D    |   R10W    |           |   R10B   |
  |    R11    |   R11D    |   R11W    |           |   R11B   |
  |    R12    |   R12D    |   R12W    |           |   R12B   |
  |    R13    |   R13D    |   R13W    |           |   R13B   |
  |    R14    |   R14D    |   R14W    |           |   R14B   |
  |    R15    |   R15D    |   R15W    |           |   R15B   |

  **Note:**

  1. Unfortunately, these are **not 68 independent registers**; instead, the x86-64 **overlays** the 64-bit registers over the 32-bit registers, the 32-bit registers over the 16-bit registers, and the 16-bit registers over the 8-bit registers.
  2. Because the general-purpose registers are not independent, **modifying one register may modify as many as other registers**.

- In addition to the general-purpose registers, the x86-64 provides specialpurpose registers, including eight floating-point registers implemented in the x87 **floating-point unit (FPU)**. Intel named these registers **ST(0) to ST(7)**. Unlike with the general-purpose registers, an application **program cannot directly access these**.

  - **Each floating-point register is 80 bits wide**, holding an extendedprecision real value (hereafter just extended precision).
  - In the 1990s, Intel introduced the MMX register set and instructions to support **single instruction, multiple data (SIMD) operations**. The **MMX register set is a group of eight 64-bit registers that overlay the ST(0) to ST(7) registers on the FPU**.Intel corrected this issue in later revisions of the x86-64 by **adding the XMM register set**.
  - To overcome the limitations of the MMX/FPU register conflicts,  AMD/Intel added **sixteen 128-bit XMM registers (XMM0 to XMM15) and the SSE/SSE2 instruction set**. Each register can be **configured as four 32-bit floating-point registers**. In later variants of the x86-64 CPU family, AMD/Intel d**oubled  the size of the registers to 256** bits each (**renaming them YMM0 to YMM15**).

- The **RFLAGS (or just FLAGS) register is a 64-bit register** that encapsulates several single-bit **Boolean (true/false) values**. most of the bits in the RFLAGS register are either **reserved for kernel mode (operating system)**.

  - Eight  of these bits (or flags) are of interest to application programmers writing  assembly language programs: the **overflow**, **direction**, **interrupt disable**, **sign**, **zero**, **auxiliary carry**, **parity**, and **carry flags**.

    <img src="../2023.5.4-2023.5.7/1.png" style="zoom:50%;" />

    **Note:**

    1. The  **RSP register**, for example, has a very special purpose that effectively prevents you from using it for anything else (**it’s the stack pointer**). Likewise, the RBP register has a special purpose that limits its usefulness as a general-purpose register. For the time being, avoid the use of the **RSP and RBP** registers for generic calculations.

### 1.5 Declaring Memory Variables in MASM

- To create (writable) data variables, you have to **put them in a data section of the MASM source file**, defined using the **.data** directive. This directive tells MASM that **all following statements (up to the next .code or other sectiondefining directive) will define data declarations to be grouped into a read/ write section of memory**. as show in the following codes snippet:

  ~~~assembly
  	.data
  label directive ?;label is the variable name
  ~~~

  label is a legal MASM identifier and directive is one of the **directives appearing in the following table**:

  <img src="../2023.5.4-2023.5.7/2.png" style="zoom:50%;" />

  The question mark (?) operand tells MASM that the **object will not have an explicit value** when the program loads into memory (**the default initialization is zero**). for example:

  ~~~assembly
  	.data
  hasInitialValue sdword -1
  ~~~

  **Note:**

  1. MASM itself usually **doesn’t care whether a variable holds a signed or an unsigned value**. MASM allows both of the following:

     ~~~assembly
     	.data
     u8 byte -1 ; Negative initializer is okay
     i8 sbyte 250 ; even though +128 is maximum signed byte
     ~~~

- It is **possible to reserve storage for multiple data values in a single data declaration directive**. this will create an **array like variable**. for example:

  ~~~assembly
  fmtstr byte "hello world",0
  ~~~

  **Note:**

  1. fmtstr plus a zero byte for the , 0 operand at the end of the string, this **terminate the string of characters**('\0').

- When MASM is processing declarations in a .data section, it **assigns consecutive memory locations to each variable**. for example:

  ~~~assembly
   .data
  i8 sbyte ?
  i16 sword ?
  i32 sdword ?
  i64 sqword ?
  ~~~

  <img src="../2023.5.4-2023.5.7/3.png" style="zoom: 50%;" />

- During assembly, MASM associates a data type with every label you define,  including variables.

  <img src="../2023.5.4-2023.5.7/4.png" style="zoom:50%;" />

- MASM allows you to declare manifest **constants** by using the **= directive**. A  manifest constant is **a symbolic name (identifier) that MASM associates with a value**. Everywhere the symbol appears in the program, MASM will **directly substitute** the value of that symbol for the symbol. this is the same Mechanism with

  ~~~assembly
  label = expression
  #define label value
  ~~~

  in C++. Most of the time, MASM’s **equ directive** is a synonym for the = directive. for example:

  ~~~assembly
  dataSize = 256 ;define a constant variable dataSize with the value 256
  dataSize equ 256 ;define a constant variable dataSize with the value 256
  ~~~

### 1.6  Some Basic Machine Instructions

- the **mov instruction assign data from one location to another**.

  ~~~assembly
  mov destination_operand, source_operand
  ~~~

  



