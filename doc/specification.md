# JadeVM Specification

## Memory Model
JadeVM's memory is separated into two, non-overlapping regions: instruction memory and stack memory.

Instruction memory is one-byte addressable and is comprised of JadeVM instructions. The Program Counter register
contains the offset of the next instruction to execute relative to the start of instruction memory. All instructions are
executed sequentially, and deviation from this order only occurs on branching instructions (`ret/jmp/jmpt/jmpf/call`).
The behavior of the execution of malformed JadeVM instructions is undefined.

Stack memory is four-byte addressable and is comprised of data utilized in most of the JadeVM instructions. The
Stack Pointer register contains the offset of the last pushed value on the stack relative to the start of stack memory.
The behavior of a stack overflow and underflow is undefined. Program arguments are pushed (in reverse-order) onto the
stack, followed by the argument count.

Both the instruction memory and stack memory are 64KiB in size, though their initial contents are unspecified.

## Registers
Register | Description     | Initial Value
---------|-----------------|--------------
pc       | Program Counter | `0`
sp       | Stack Pointer   | `1 + argc`

## Instruction Reference
Opcode | Mnemonic   | Description
-------|------------|-------------------------------
0x00   | hlt        | `exit();`
0x01   | nop        | `;`
0x02   | neg        | `push(-pop());`
0x03   | add        | `x = pop(); push(pop() + x);`
0x04   | sub        | `x = pop(); push(pop() - x);`
0x05   | mul        | `x = pop(); push(pop() * x);`
0x06   | div        | `x = pop(); push(pop() / x);`
0x07   | mod        | `x = pop(); push(pop() % x);`
0x08   | not        | `push(~pop());`
0x09   | or         | `x = pop(); push(pop() | x);`
0x0A   | and        | `x = pop(); push(pop() & x);`
0x0B   | xor        | `x = pop(); push(pop() ^ x);`
0x0C   | shl        | `x = pop(); push(pop() << x);`
0x0D   | shr        | `x = pop(); push(pop() >> x);`
0x0E   | pop u8     | `sp -= u8;`
0x0F   | ret        | `pc = pop();`
0x10   | jmp s16    | `pc += s16;`
0x11   | jmpt s16   | `if (pop()) pc += s16;`
0x12   | jmpf s16   | `if (!pop()) pc += s16;`
0x13   | call s16   | `push(pc); pc += s16;`
0x14   | eq         | `x = pop(); push(pop() == x);`
0x15   | neq        | `x = pop(); push(pop() != x);`
0x16   | lt         | `x = pop(); push(pop() < x);`
0x17   | le         | `x = pop(); push(pop() <= x);`
0x18   | gt         | `x = pop(); push(pop() > x);`
0x19   | ge         | `x = pop(); push(pop() >= x);`
0x1A   | get s16    | `push(memory[sp + s16]);`
0x1B   | set s16    | `memory[sp + s16] = pop();`
0x1C   | imm s16    | `push(s16);`