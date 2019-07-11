---
title: "Read Assembly"
categories:
  - Language
tags:
  - OS
  - debug
  - assembly
  - notes
toc: true
---

Some notes to facilitate the reading of *assembly* code in IA32.

# IA32 Registers

Machine code views the memory as a large, byte-addressable array. Aggregated data types in C are represented in machine code as contiguous collections of bytes. A pointer in C just saves the address of the first byte of the block.

An IA32 CPU contains 8 *registers* storing 32-bit values. They have peculiar names and usage. [X86-assembly/Registers](https://www.aldeid.com/wiki/X86-assembly/Registers) gives a synthesis on them.

Name | Usage | Comment
----------------|----------------|----------------
`%eip` | Program counter | Indicates the address in memory of the next instruction to be executed.
`%esp` | Stack pointer | Holds the address of the *top* element in the stack (as the stack grows downward, this is the bottom)
`%eip` | Frame pointer | Holds the address of the first element in the current stack frame

## Access

Three types of values accesses in assembly

- *Immediate value* is for constant value, e.g. `$4` is to take the value `4`
- *register* denotes the content of of one of the register, e.g. `%eax` takes the 32 bits data in the register denoted by `%eax`
- *Memory reference* designates a value stored in memory, it is like pointer in C, e.g. `(%eax)` is to take the value at the address saved in `%eax`, this is called as argument in a command, it always comes with size of bytes to read.

The third case may can be more complicated than the other two in practice, since it can involve memory access both to registers and to memory. For example, for `movl (%eax),%edx`, we first locate the byte at the address `%eax` in memory, then copy 4 bytes from address `%eax` to `%eax+3` to the register `%edx`. By the way, we cannot have both source and destination be memory reference.

**Note:** It worth noticing that when we add values of one register to another like `addl %eax,%edx`, it is one single operation. In contrast, `addl %eax,(%edx)` yields multiple operations: we first load the value at the address `%edx` from memory to CPU register, then add the value `%eau`, finally store back the result into memory. The former instruction has much higher performance.
{: .notice--warning}

Another commun format to access memory is to use *offset* and *scaling factor*. Like `movl 4(%esp),%eax` is to copy the 4 bytes value starting from the address `%esp+4` in memory to the register `%eax`.

**Note:** **Pointer** Dereferencing a pointer in C involves copying that pointer into a register, and then use this register to take the value in memory. As the access to register is much faster than to memory, this understanding can help us to further optimize our code with compound types.
{: .notice--info}

# Instructions

All the lines beginning with `.` are directives to guide the assembler and linker.

## Suffix

- `b` = 1 byte, e.g. `char`
- `w` = 2 bytes, *word*, e.g. `short`
- `l` = 4 bytes, *double words*, e.g. `int`
- `q` = 8 bytes, *quad words*, e.g. `double`

Suffix are often attached to command like `movw` (move word).

Instruction | Format | Meaning
----------------|----------------|----------------
mov | `movl S,D` | copy 4 bytes source value to destination
push | `pushl S` | decrement `%esp` by 4 **and** copy the 4 bytes source value to `(%esp)` 
pop | `popl D` | copy the 4 bytes value stored at `%esp` to `D` **and** increment `%esp` by 4 


# Stack Frame Structure

IA32 use program stack to support procedure calls. The stack is used to pass proceduree arguments and to store return information etc. The portion of the stack allocated for a single procedure call is called a *stack frame*, delimited by `%ebp` and `%esp`.