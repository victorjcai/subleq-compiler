# Subleq Compiler for Floating Point

A [Subleq](https://esolangs.org/wiki/Subleq) compiler for 32-bit integer and IEEE-754 Floating Point. 

## Supported Capabilities

- Function calling support in Mazonka's Subleq assembly syntax
- Int32 multiplication and division functions
- FP32 addition, subtraction, multiplication, and division functions
- Int32 to FP32 conversion 
- Variable-input right shift
- Hexadecimal input and output functions, bypassing hsq's ASCII interpreter

A [Pi calculation benchmark](https://gitlab.cba.mit.edu/pub/pi/-/blob/master/pi.pdf) is also included. 

## What is Subleq? 

**Subleq** (**sub**tract and branch if **l**ess than or **eq**ual to zero) is perhaps the best-known One-Instruction Set Computer (OISC), or Ultimate Reduced Instruction Set Computer (U-RISC). It is a Turing-complete in a single instruction that takes three integer inputs a, b, c, subtracts the integer at address a from the integer at address b, stores the result at address b, then if the result is less than or equal to 0, conditionally branches to address c. 

Here is a simple Subleq interpreter in pseudocode for ``SUBLEQ A B C``:
```
memory[B] = memory[B] - memory[A]
if memory[B] <= 0 then goto C
```
In other words, Subleq consists of only an integer subtraction and a branch. 

### Primer: Mazonka's Subleq Assembly Syntax
In Mazonka's Subleq assembly (asq) syntax, a typical instruction is simply written as three symbols ("variables") in an empty line, each pointing to a memory location elsewhere in the program. With only one instruction, no opcode is needed. 
```
a b c
```
The address of the next memory word can be referenced as ``?``, and can be added or subtracted to to reference nearby words. For instance, in the program below, if the branch is taken in the first instruction, then the third instruction would be run next, skipping the second instruction:
```
a b ?+3
d e f
g h i
```
Here is a useful shorthand for unconditional subtraction of the form ``a b ?``, equivalent to pseudocode ``b = b - a``. It can be written as
```
a b
```
Another useful shorthand is the unconditional variable reset of the form ``a a ?``, equivalent for pseudocode ``a=0``. It is written as
```
a
```

Program data can be inserted using a ``.`` and referenced by name. for instance, for the programs above, you would need to define the symbols for a through i:
```
. a:0
. b:0
. c:0
```
and so on. A halt instruction is written as a jump to a negative address, particularly -1: ``a a (-1)``. To prompt for input into ``b``, make the first argument -1: ``(-1) b``. To output an ASCII character from ``a``, make the second argument -1: ``a (-1)``. Note that these input and output conditions differ from operating on a symbol whose value at its address happens to be -1. 


## Installation

This Subleq Compiler is written in [Mazonka et al.'s](https://arxiv.org/abs/1106.2593) Subleq assembly syntax, including the two- and one-argument shorthands. We bypass Mazonka's C-style compiler in favor of low-overhead assembly-level algorithms, and run using Mazonka's Subleq assembly (asq) assembler. 

1. Pull this repository.

2. Get Mazonka's assembler (hsq.cpp) from [here](http://mazonka.com/subleq/hsq.html) [(archived)](https://web.archive.org/web/20230605204507/http://mazonka.com/subleq/hsq.html). 

3. Compile to executable:
```bash
gcc hsq.cc -o hsq
```

## Usage

To run the pi calcuation benchmark, run
```bash
./hsq subleq_compiler_pi.asq
``` 
You will be prompted for the number of iterations to run. Enter a number in hexadecimal (e.g., 1000). The [Pi calculation benchmark](https://gitlab.cba.mit.edu/pub/pi/-/blob/master/pi.pdf) requires 5 FLOPs plus a type conversion per iteration. 

The program outputs the sign, magnitude, and exponent fields of the IEEE-754 floating point output, in hexadecimal. True pi looks like:
- s = 0x00000000
- m = 0x00c90fdb
- e = 0x00000080


## Release

This work was conducted by Victor Cai under supervision of Alex Wynn at MIT Lincoln Laboratory. The pi benchmark here has been released for public usage. If you use this compiler, please credit this repository at [https://github.com/victorjcai/subleq-compiler](https://github.com/victorjcai/subleq-compiler). 

## Acknowledgement

This compiler was inspired by various works on Subleq before it, including:

- [Mazonka and Kolodin's A Simple Multi-Processor Computer Based on Subleq](https://arxiv.org/abs/1106.2593)
- [jaredkrinke's Single Instruction Computer (SIC-1) Subleq game](https://jaredkrinke.itch.io/sic-1)
- [agp.cooper's Simple Compiler for Home Brew CPUs](https://hackaday.io/project/25583-simple-compiler)
