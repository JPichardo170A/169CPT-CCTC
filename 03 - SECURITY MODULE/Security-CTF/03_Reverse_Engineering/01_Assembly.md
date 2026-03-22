# Reverse Engineering - Assembly Challenges

Assembly language questions covering x86 and x86-64 registers and instructions.

---

## Question 2 – RAX/EAX

**Level:** 1
**Points:** 2

**Prompt:**
> What is %RAX and %EAX main purpose?

Choose the correct statement from the options below:
- Their purpose is to hold the memory address of the top of the stack
- %RAX/%EAX are not valid x86-64 registers
- They are the first return registers for x86-64 and x86
- These registers act as breakpoints when debugging a process

### Reference Intelligence

- %RAX is the 64-bit return value register in x86-64 architecture
- %EAX is the 32-bit return value register in x86 architecture
- These registers are used to store function return values
- Commonly used in calling conventions for returning integers

### Steps Taken

1. Reviewed x86 and x86-64 architecture documentation
2. Verified register roles in function return conventions
3. Cross-referenced with Intel Developer Manual and GCC calling convention
4. Confirmed %RAX and %EAX are valid and used for return values

### Verified Answer

They are the first return registers for x86-64 and x86

### Notes

- %RAX and %EAX are critical in understanding low-level function behavior
- Misidentifying these registers can lead to incorrect assumptions in reverse engineering
- Refer to Section 2.2 in Student Resources for register usage in calling conventions

---

## Question 3 – RIP/EIP

**Level:** 1
**Points:** 2

**Prompt:**
> What is %RIP and %EIP main purpose?

Choose the correct statement from the options below:
- They are the first return registers for x86-64 and x86
- %RIP/%EIP are the 64 bit and 32 bit instruction pointers that hold the memory address to the next instruction
- %RIP/%EIP are not valid x86-64 registers
- Their purpose is to keep track of the top of the stack

### Reference Intelligence

- %RIP is the 64-bit instruction pointer register in x86-64 architecture
- %EIP is the 32-bit instruction pointer register in x86 architecture
- These registers hold the memory address of the next instruction to execute
- Critical for program flow control and branching

### Steps Taken

1. Consulted x86 and x86-64 architecture references
2. Verified the role of instruction pointer registers in program execution
3. Cross-checked with Intel manuals and assembly programming guides
4. Confirmed %RIP and %EIP hold address of next instruction to execute

### Verified Answer

%RIP/%EIP are the 64 bit and 32 bit instruction pointers that hold the memory address to the next instruction

### Notes

- Understanding instruction pointers is essential for debugging and reverse engineering
- These registers differ from return registers like %RAX/%EAX
- Refer to Section 4.1 in Student Resources for instruction pointer details

---

## Question 4 – RBP/EBP

**Level:** 1
**Points:** 2

**Prompt:**
> What is %RBP and %EBP main purpose?

Choose the correct statement from the options below:
- %RBP/%EBP keep track of the top of the stack
- They are the 64 bit and 32 bit stack base pointers
- These registers act as breakpoints when debugging a process
- The purpose of these registers is to perform extended floating point math

### Reference Intelligence

- %RBP is the 64-bit base pointer register in x86-64 architecture
- %EBP is the 32-bit base pointer register in x86 architecture
- These registers are used to reference the base of the current stack frame
- Essential for stack frame management during function calls

### Steps Taken

1. Reviewed documentation on x86 and x86-64 calling conventions
2. Confirmed stack frame organization and base pointer usage
3. Verified register roles in assembly and debugging manuals
4. Clarified distinction between base pointer and stack pointer registers

### Verified Answer

They are the 64 bit and 32 bit stack base pointers

### Notes

- %RBP/%EBP help in maintaining stack frames for local variable access
- Modern compilers sometimes omit frame pointers for optimization
- Refer to Section 5.3 in Student Resources for detailed stack frame handling

---

## Question 5 – R8

**Level:** 1
**Points:** 2

**Prompt:**
> What is %R8 size in bits?

### Reference Intelligence

- %R8 is a general-purpose register introduced in x86-64 architecture
- It is one of the extended registers beyond the original x86 set
- All extended registers in x86-64 are 64 bits in size
- Commonly used for passing function arguments and general computation

### Steps Taken

1. Reviewed x86-64 architecture register specifications
2. Verified register size in Intel and AMD architecture manuals
3. Cross-checked usage in assembly language examples and compiler output
4. Confirmed %R8 is a 64-bit register

### Verified Answer

64 bits

### Notes

- %R8 through %R15 are exclusive to x86-64 and not present in 32-bit x86
- These registers expand the available general-purpose register set
- Refer to Section 6.1 in Student Resources for register size and usage

---

## Question 6 – Register Size

**Level:** 1
**Points:** 2

**Prompt:**
> Which of these registers has a size of 32 bits?

Choose the correct register from the options below:
- %RAX
- %AX
- %R12D
- %R15W

### Reference Intelligence

- %RAX is a 64-bit register
- %AX is a 16-bit register (lower half of %EAX)
- %R12D is the 32-bit version of the %R12 register
- %R15W is a 16-bit version of the %R15 register

### Steps Taken

1. Reviewed x86-64 register naming conventions and suffix meanings
2. Verified register sizes based on suffix: D = 32-bit, W = 16-bit
3. Cross-referenced Intel architecture documentation
4. Confirmed %R12D is the correct 32-bit register

### Verified Answer

%R12D

### Notes

- Suffixes help identify register size: D (32-bit), W (16-bit), B (8-bit), no suffix (64-bit)
- Understanding register sizes is essential for accurate assembly programming and debugging
- Refer to Section 6.4 in Student Resources for register naming conventions

---

## Question 7 – JE

**Level:** 1
**Points:** 2

**Prompt:**
> What register does the JE instruction rely upon?

### Reference Intelligence

- JE (Jump if Equal) is a conditional jump instruction in x86/x86-64 assembly
- It relies on the Zero Flag (ZF) in the EFLAGS register
- The Zero Flag is set when a comparison or subtraction results in zero
- JE checks ZF to determine whether to jump to the target instruction

### Steps Taken

1. Reviewed x86 instruction set documentation
2. Verified JE behavior in Intel and AMD architecture manuals
3. Analyzed flag register usage in conditional jumps
4. Confirmed JE relies on the Zero Flag within the EFLAGS register

### Verified Answer

EFLAGS (specifically the Zero Flag - ZF)

### Notes

- JE is commonly used after CMP or SUB instructions
- If ZF = 1, JE will jump to the specified label or address
- Refer to Section 7.2 in Student Resources for flag-based control flow

---

## Question 8 – JE 2

**Level:** 1
**Points:** 2

**Prompt:**
> What flag does the JE instruction rely upon?

Choose the correct flag from the options below:
- Parity Flag
- Zero Flag
- Sign Flag
- Carry Flag

### Reference Intelligence

- JE (Jump if Equal) checks the Zero Flag (ZF) in the EFLAGS register
- ZF is set when a comparison or subtraction results in zero
- JE will jump to the target instruction if ZF = 1
- Other flags like Parity, Sign, and Carry are used by different conditional jumps

### Steps Taken

1. Reviewed JE instruction behavior in x86/x86-64 manuals
2. Verified flag dependencies for conditional jumps
3. Cross-referenced with Intel Developer documentation
4. Confirmed JE relies on the Zero Flag

### Verified Answer

Zero Flag

### Notes

- JE is often used after CMP or SUB instructions
- ZF = 1 indicates operands were equal, triggering the jump
- Refer to Section 7.3 in Student Resources for flag-based branching logic

---

## Question 9 – CMP

**Level:** 1
**Points:** 2

**Prompt:**
> What does the CMP instruction do?

Choose the correct statement from the options below:
- Move source to destination
- Pushes the source onto the stack
- Jump to specified location
- Compares 2 values via subtraction

### Reference Intelligence

- CMP is a comparison instruction in x86/x86-64 assembly
- It subtracts the source operand from the destination operand
- The result is not stored, but flags in the EFLAGS register are updated
- These flags (e.g., Zero Flag, Sign Flag) are used by conditional jumps like JE, JNE, etc.

### Steps Taken

1. Reviewed x86 instruction set documentation
2. Verified CMP behavior in Intel Developer Manual
3. Analyzed flag-setting behavior post-comparison
4. Confirmed CMP performs subtraction for comparison without storing result

### Verified Answer

Compares 2 values via subtraction

### Notes

- CMP is often followed by conditional jumps that rely on flag states
- It's essential for control flow and decision-making in assembly programs
- Refer to Section 7.5 in Student Resources for CMP usage and flag interactions

---

## Question 10 – ASM Flow 1

**Level:** 1
**Points:** 2

**Prompt:**
> ```
> Main:
>    Mov R8, 25
>    Push R8
>    Mov R10, 50
>    Push R10
>    Pop RAX
> ```
> What value is on the top of the stack?

### Reference Intelligence

- Stack operates in LIFO (Last In, First Out) order
- `Push R8` places 25 on the stack
- `Push R10` places 50 on top of 25
- `Pop RAX` removes 50 from the stack and stores it in RAX
- After the pop, 25 becomes the new top of the stack

### Steps Taken

1. Analyzed instruction flow and stack behavior
2. Traced push/pop operations and register values
3. Verified stack pointer movement and value placement
4. Confirmed final stack state after pop operation

### Verified Answer

25

### Notes

- Stack manipulation is key in function calls and temporary storage
- Always consider LIFO behavior when tracing stack operations
- Refer to Section 8.1 in Student Resources for stack flow examples

---

## Question 11 – ASM Flow 2

**Level:** 1
**Points:** 2

**Prompt:**
> ```
> Main:
>    Mov R8, 25
>    Push R8
>    Mov R10, 50
>    Push R10
>    Pop RAX
> ```
> What value is in the return register?

### Reference Intelligence

- `Push R8` places 25 on the stack
- `Push R10` places 50 on top of 25
- `Pop RAX` removes the top value (50) from the stack and stores it in RAX
- RAX is the primary return register in x86-64 architecture

### Steps Taken

1. Traced stack operations in sequence
2. Verified values pushed and popped
3. Confirmed RAX receives the last pushed value
4. Validated RAX as the return register in x86-64

### Verified Answer

50

### Notes

- RAX is used to hold return values from functions in x86-64
- Stack operations follow LIFO behavior, making the last pushed value the first popped
- Refer to Section 8.2 in Student Resources for return register behavior and stack tracing

---

## Question 12 – ASM Flow 3

**Level:** 1
**Points:** 2

**Prompt:**
> ```
> Main:
>    Mov R9, 5
>    Mov R10, 20
>    Add R10, R9
>    CMP R10, R9
>    JE Clean
>    Mov RAX, 14
>    ret
>
> Clean:
>    Mov RAX, 0
>    ret
> ```
> What value is returned from this flow?

### Reference Intelligence

- `Mov R9, 5` -> R9 = 5
- `Mov R10, 20` -> R10 = 20
- `Add R10, R9` -> R10 = 25
- `CMP R10, R9` -> Compares 25 to 5 -> ZF = 0 (not equal)
- `JE Clean` -> Not taken since ZF != 1
- Execution continues to `Mov RAX, 14` and `ret`
- Clean label is skipped

### Steps Taken

1. Traced register assignments and arithmetic operations
2. Evaluated CMP result and Zero Flag status
3. Determined JE condition outcome
4. Identified final value loaded into RAX before return

### Verified Answer

14

### Notes

- Conditional jumps like JE depend on flag states set by CMP
- Since R10 != R9, the jump to Clean is not executed
- Refer to Section 8.4 in Student Resources for control flow analysis
